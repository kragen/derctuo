It looks like a new way to use transactional memory can simultaneously
improve programming in a large number of very important ways: improved
debugging, simplifying some of the hardest parts of JIT compilation,
dramatically simplified error handling, fearless concurrency, improved
interactive responsiveness (but I repeat myself), modular blocking on
input, transparent incrementalization, simple and fast parsing, and
enormously faster generative testing and solving of inverse problems.

How does this work?

Suppose that you have an imperative programming language
[like Daira Hopwood's Noether][0]
in which
every function call is associated with a new nested transaction, one
covering all mutable variables and other effects, and your normal means of handling
errors is by rolling back these transactions.  What does that give you?

[0]: https://www.thestrangeloop.com/2013/noether-symmetry-in-programming-language-design.html

This seems like a way to mostly cut the knot of error handling and
responsiveness, without requiring static bounds of worst-case
execution time for your entire user interface.

Debugging
---------

Well, one thing it gives you is radical debuggability: because every
function call you enter has to save enough information for
backtracking if it needs to roll back.  The debugger can see this
information, and it can restart the function from the beginning as if
it had not started running
(Hopwood calls this "reversible execution" in hir 2014 Strange Loop
presentation, crediting the idea to a 1973 paper by Marvin Zelkovitz).
This enables efficient granular
time-travel debugging, but also, it's potentially useful simply to
look at the pending changes so far made by each of the functions on
the stack so far.  And implementing edit-and-continue in the debugger
becomes substantially easier under some circumstances when you can
restart the function you've just edited.  Also, being able to see
which transactional variables are being *depended on* at each level in
the call stack is also a potential boon to debugging, sort of like
`strace` at a per-function level.

JIT support
-----------

Rolling back to the beginning of the function and re-executing it is
also a particularly simple way to support on-stack replacement
(whether deoptimization for debuggability, or optimization to get a
speedup on a hot loop that might not run again).

Error handling
--------------

This was Hopwood's primary concern in the design of Noether.

Error handling becomes substantially easier.  Nonlocal exceptions are
especially popular in pure functional languages because cleanup while
unwinding the stack is unnecessary; by contrast, C++ had so much trouble with this
that the STL wasn't exception-safe for several years after it was written!  In fact, if I
understand correctly, exceptions are still prohibited at Google,
because they complicate reasoning about what happens in failure
cases — precisely what kinds of states can result.  But in such a
transactional system, the transaction system takes care of cleaning up
any incompletely made changes.  So you don't need RAII, destructors,
or special failure handling.

The basic nested-transaction feature doesn't require tracking *reads*
of transactional variables, the way Haskell's STM does, only *writes*.
That's because there's no need to check a transaction for validity
when you go to commit it — no other code could have been running
concurrently.  You only need to buffer the writes to transactional
variables so that you can undo them if you have to roll back.

(As Hopwood points out in zir 2014 Strange Loop presentation, logging
writes in this way is also what you need for a concurrent or
generational garbage collector.)

Fearless concurrency
--------------------

However, if you do additionally track reads of transactional
variables, you can use the transaction system for multithreading with
a guarantee of serializability.  This is probably only practical if
the language is mostly functional, like Clojure or OCaml, and only
slightly imperative, because pervasive Python-style mutability would
entail logging a huge amount of read traffic to the mutable variables,
similar to the overhead of unoptimized reference counting.  The
per-call transactions would reduce the cost of retrying in most cases.

There's the question of when the threads of such a multithreaded
program would *not* be in a transaction, making their transactional
mutations visible to other threads.  I think the answer is something
like Erlang's top-level process loop, where the process evolves by
having its top-level procedure make a tail call to itself, and of
course when a thread exits successfully.

Such a system would be sort of like the "dynamic typing" equivalent of
Rust's fearless concurrrency through lifetime checking: your program's
non-interference is checked dynamically at run-time, and corrected if
necessary, rather than proven at compile-time.

Modular blocking
----------------

You might think that this approach would preclude I/O anywhere but at
some sort of top-level event loop, at least per thread, since I/O is a
side effect.  It's straightforward to see how you could buffer up
output (maybe logging it for debugging in case of an abort) until the
top level is reached, but how could you do that for input?

Fortunately _Composable Memory Transactions_ has a solution to taking
input: if we log reads, as a multithreaded system would, then an input
routine such as getchar() would simply `retry` if no input character
was waiting.  This would abort its transaction, but the transaction
system would know that it would simply fail again if no input
character was waiting, since it failed by calling `retry` instead of
having a read/write conflict or an error.  Its caller has the option
(as, one supposes, it would have in the case of errors) to handle the
retry by moving on to a fallback case, for example reading from a
different input stream.  If at some point the whole shebang fails, the
transaction system can suspend the thread (and do other work, if
applicable) until one of the things it had read before `retry`ing
changes.  (This is the point where handling diverges from ordinary
errors: if the handler for an ordinary error also fails, you just
unwind the transaction stack until you terminate the program.)

This provides, in the words of the paper, "a modular form of
blocking" — a thread can wait on a condition variable, or an arbitrary
Boolean function of various transactional variables, or anything else
that can be shoehorned into the transaction system, including input
events — and the functions that do such waiting can be made
nonblocking by having a fallback that always succeeds, or combined by
falling back from one to the other.

Safe aborting for guaranteed responsiveness
-------------------------------------------

Another benefit provided by pervasive transactionality — and this one
wouldn't require either read-logging or nested transactions — is that
any task can always be safely aborted, which eliminates the Sophie's
Choice we normally face in event-loop systems where we can get either
safety from concurrency problems (by running code in the event-loop
thread) or guaranteed responsiveness (by running code in another
thread).  If an event handler is running when another higher-priority
event comes in, we can simply peremptorily discard the current
transaction, including the dequeuing of its input event, and launch
the handler for the higher-priority event.  (A classic case of this is
repainting the screen in response to an input keystroke when another
input keystroke comes in, which will probably require an additional
screen repaint.)  Or, if we *do* do read logging, we can run one
thread for each concurrently executing event handler, retrying
executions as necessary.

This kind of abandonment can be constant-time, but only if the
buffered writes from the transaction are not written to their home
location; as Hopwood points out in hir talk slides, if the writes are
written to their home locations, then rolling back a transaction
requires undoing all the writes, one by one.  An alternative that
provides constant-time, effectively instantaneous, abandonment is to
only write the writes to their home locations when a (top-level)
transaction commits.  This requires every read of a transactional
variable to check for a buffered write belonging to the current
transaction before falling back to the value from the home location.

With virtual memory, one common problem for responsiveness is that
when the system starts to thrash, responsiveness for the whole user
interface goes to hell, because there's no reasonable way to make
progress when your threads are blocked on page faults.  If, instead,
page faults are handled by failing a transaction as needing to
retry — just as if it were blocking on input — it should be possible
to try many different event handlers, bringing all of their working
sets into memory, and allowing whichever ones can make progress to do
so without being blocked by the others that are blocked on page
faults.  This, again, could be done in a single-threaded event-loop
system that just uses one transaction per event handler, rather than
one transaction per function.  (However, it might make things worse
rather than better, and of course requires integration with the OS
kernel.)

Error values
------------

With regard to error handling, it might be best in most cases for
aborted functions to return error values rather than automatically
propagating.  As long as these error values are either handled
(inspected to see what the error is, presumably as part of a
conditional) or moved to some kind of storage (for later debugging),
automatic propagation woud be suppressed, as in Wheat.  But if such an
error value is ignored (evaluated in void context, or stored in a
variable whose lifetime ends without being tested) it would propagate
up to the parent function.

These error values can propagate along the program's dataflow graph,
like floating-point quiet NaNs; they only leap over to the
control-flow graph if they are "leaked" or "dropped".

Modal reasoning
---------------

Another application of transaction rollback is *code search*, as
suggested by Hopwood in hir 2014 talk under the heading "confining
side effects", based on Joel Galenson's† [CodeHint] (which cites the
Squeak method finder): is there an existing function in my code base
that will convert 4 and 66 into "iv" and "lxvi" respectively?  How
about a composition of two functions?  Or five methods?  An obvious
way to implement such a query is to just run all the functions, or
pairs of functions, and see what you get, but to do this safely you
need to prevent the functions from looping infinitely or causing
destructive side effects.  By running them inside a transaction and
killing them if they exceed a time limit, you can test them safely.

(Note, though, that this time limit is a potentially deadly inlet
through which nondeterminism could enter the system, causing any
computation that depends on such testing to be irreproducible; if it
counts something like function calls plus backward control flow
transfers and is precise, it's safe, but not if it's counting
wall-clock time or clock cycles and/or is checked only irregularly.)

A generalization of this is the ability for a program to reason about
code's behavior under conditions that do not presently prevail, simply
by running it inside a transaction that is then rolled back.  This
does require the transaction's rollback notification to contain enough
information to tell us what we want to know about the code's behavior,
but that's probably a requirement for useful transaction failure
messages, anyway.

[CodeHint]: http://people.eecs.berkeley.edu/~bjoern/papers/galenson-codehint-icse2014.pdf

Given this kind of facility, you could reasonably ask questions such a
the following: Which methods would write to some field of this object?
Is there any live object on which calling the ".open()" method would
read the current user ID?  What is the object whose ".destroy()"
method would return the highest value?

In the debugger context, this kind of automatic cleanup would allow
you to view "speculative" executions as well: the hypothetical flow of
values through a piece of code, without the risk of corrupting the
"true" state of the program under inspection with a side effect.

† and Philip Reames's, and Rastislav Bodik's, and Björn Hartmann's,
and Koushik Sen's CodeHint

Memoization and incrementalization
----------------------------------

Suppose the transaction for a function
is logging all its reads and writes of mutable data; if it
additionally logs which function it is, any closed-over data, its
input parameters, then it becomes possible to use it for
memoization — any call to the same function with the same parameters
and closure data will necessarily perform the same writes and return
the same value, unless one of those reads is out of date, or execution
is nondeterministic.  So it's
valid to just perform those writes and return those results without
actually running any of the function's code.  This is very similar to
a build system like `make`, or to Umut Acar's "Self-Adjusting
Computation"; it provides a way to transparently incrementalize a
computation, so that it can be efficiently re-executed on slightly
modified input.  Also, it automatically derives a
guaranteed-linear-time Packrat parser from an ordinary
exponential-time recursive-descent parser.

Moreover, this caching or memoization is still valid *even if the
original memoized computation was a child of a transaction that was
rolled back*.  That is, even computation that was "discarded" can
affect the memo table.  (This is the same mechanism that produced the
Spectre and Meltdown vulnerabilities in Intel CPUs — it can produce a
subliminal leak of information.)  This means that we can speculatively
pre-cache computations we expect to need in the future.

Incrementalization is an extremely important transformation for a few
different reasons:

1. By reducing the need for manual state management for efficiency, it
   can make direct programs much simpler.  For example, you could
   implement a word processor as a view function from document state
   to view state, a window function from view state to pixel state,
   and an edit function from (document state, input event) pairs to
   document state, or perhaps even a function from input histories
   (keystroke sequences) to rectangles of pixels.

2. By making coordinate search practical, it can make many programs
   invertible in practice, permitting the practical solution of a wide
   variety of inverse problems.  The optimization procedure can
   randomly alter the program's input, propagating the incremental
   changes through the incrementalized program, in order to converge
   on the desired result.

3. A special case of the former is generative software testing like
   that done by Hypothesis or American Fuzzy Lop, where the "desired"
   output is a crash or assertion failure; this is to some extent how
   AFL works, but because it can only backtrack chronologically, its
   strategies for exploring the input space are necessarily limited.
   Once a failure is found, incrementalization also greatly
   accelerates the test-case minimization process.  Additionally, the
   introspection provided by the transaction system can be used by the
   generative testing system to guide its search.

4. Another special case, one which might not work out, is
   superoptimization — search over a space of *programs* for the
   shortest or fastest program that has the desired effect.  This
   shades into the "code search" application mentioned earlier.

In short, incrementalization reduces the need for explicit caching and
makes searching over the space of executions immensely more efficient.

Integrity enforcement
---------------------

Hopwood also describes the use of such write logging to help with
invariant maintenance: the write log tells you which objects have been
changed in a transaction and whose state thus ought to be checked for
correctness, and transaction rollback gives you the wherewithal to
undo the damage.  This is of course precisely the "C" in "ACID" in the
traditional RDBMS usage of transactions: transactions violating
consistency constraints will not be committed.  (Ze also suggests
automatic failover to alternate implementations in order to either
detect the bug more precisely, by using slower invariant checking, or
to fail over to an inefficient but trivially-correct implementation of
the mutation.)

The incremental computation framework described in the previous
section provides an efficient and simple way to do this: before
committing, the code in the top-level transaction invokes a procedure
which ostensibly verifies all the interesting invariants in the entire
part of the system that it knows about, failing otherwise.  This
procedure invokes many other procedures to check invariants on
particular parts of the system; most of these procedures will not have
changed their inputs since the last invocation, and thus can succeed
instantly simply using the memo table.  But those which read
transactional variables that have been written to will run for real,
giving the transaction a chance to fail.

Optimizing transactions
-----------------------

If we're *only* using transactions for error recovery and/or
peremptory work discarding for responsiveness (not memoization,
multithreading with optimistic synchronization, deoptimization, or
debugging, as suggested above), then, when a parent procedure invokes
a child procedure at a callsite where failures in the child will
necessarily propagate to a failure in the parent, it's not necessary
(for execution) to preserve the separate transaction for the child
procedure — if the child rolls back, the parent rolls back too.  This
optimization dramatically reduces the amount of extra work imposed by
the transaction system.

A subroutine can mutate its local variables freely without incurring
any transaction overhead, unless those variables are referenceable
(something impossible in, for example, Scheme) and references to them
have in fact escaped.  For example, Pascal-style `var` parameters can
enable references to local variables to be passed to callees, but the
language guarantees that once the callees return, those references are
no longer live.

Plumbing transactions to the user interface, the filesystem, and the network
----------------------------------------------------------------------------

Depending on what filesystem you're running and how deeply you've been
hurt, you might be able to trust the filesystem to honor your
transaction boundaries as well, which means that code inside a
transaction can read and write the filesystem freely — but the
filesystem must give us a way to keep the writes within a
transactional bubble, hidden from the rest of the world at first, and
perhaps forever.  Also, it must give us a way to transactionally
validate our reads when we go to commit, if there's a possibility the
data we read has been modified in the meantime.

This is potentially useful because it means you can run a transaction
that includes multiple programs all communicating through the
filesystem.  This also potentially means you can use this sort of
fearless concurrency in things like shell scripts, avoiding the messy
failure cases and concurrency problems that normally plague them.

(If you do this with memoization of program outputs, you have a rather
standard build system.)

A network file server can participate in your transactions in the same
way as a local filesystem.  Indeed, a network server need not be
implementing anything very similar to a filesystem; it just needs to
be participating in a transactional protocol with you, either
arbitrating transaction commits and serialization or faithfully
deferring to some such arbitration system.  A queueing system is a
prime candidate.

If you're willing to embrace the filesystem and networked services as
part of your transactions, what about users?  In particular, if you
can run multiple entire programs inside a giant transaction, you could
enable users to create a long-lived transaction that they then have a
window into, as a way to experiment with new states they may not want
to keep.  However, I'm not sure this approach can really deliver a
usable user experience of undo and restoration from backups.  However,
using this approach for debugging implies that it's possible for users
to see inside an uncommitted transaction, at least within the
debugger.
