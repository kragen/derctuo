It looks like a new way to use transactional memory can simultaneously
improve programming in a large number of very important ways: improved
debugging, simplifying some of the hardest parts of JIT compilation,
dramatically simplified error handling, fearless concurrency, improved
interactive responsiveness (but I repeat myself), modular blocking on
input, transparent incrementalization, simple and fast parsing, and
enormously faster generative testing and solving of inverse problems.

How does this work?

Suppose that you have an imperative programming language
[like Daira Hopwood’s Noether][0]
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
(Hopwood calls this “reversible execution” in [hir 2014 Strange Loop
presentation][2], crediting the idea to a [1973 paper by Marvin Zelkowitz][3];
ze claims that Zelkowitz found time overheads of less than a factor of 2
for PL/I, which features pervasive mutability.  Zelkowitz seems to have
done his 1971 dissertation, “Reversible execution as a diagnostic tool,”
on the topic, at Cornell, though I could only find [a 13-page tech report][4]).
This enables efficient granular
time-travel debugging, but also, it’s potentially useful simply to
look at the pending changes so far made by each of the functions on
the stack so far.  And implementing edit-and-continue in the debugger
becomes substantially easier under some circumstances when you can
restart the function you’ve just edited.  Also, being able to see
which transactional variables are being *depended on* at each level in
the call stack is also a potential boon to debugging, sort of like
`strace` at a per-function level.

[2]: https://github.com/noether-lang/noether/tree/master/doc/presentations/StrangeLoop2014
[3]: http://www.cs.umd.edu/~mvz/pub/zelkowitz-cacm-1973.pdf "Reversible Execution, CACM, September 1973, volume 16, number 9"
[4]: https://ecommons.cornell.edu/xmlui/bitstream/handle/1813/5967/71-92.ps?sequence=2 "Reversible execution as a diagnostic tool (preliminary draft), Cornell CS TR 71-92, January 1971"

JIT support
-----------

Rolling back to the beginning of the function and re-executing it is
also a particularly simple way to support on-stack replacement
(whether deoptimization for debuggability, or optimization to get a
speedup on a hot loop that might not run again).

For example, if after entering a slow interpreted procedure, the JIT
found that it had spent a lot of time in that procedure without
finishing, because it contains a long loop.  The on-stack replacement
problem is that, even if the JIT compiles a fast native-code version
of the procedure, the interpreter is still in the middle of running
the slow version.  To get the benefit of the compilation, it somehow
needs to transform a state of the interpreted version (register
settings, program counter, etc.) into a corresponding state of the
native-code version.  Transactions give us the alternative possibility
of rolling back from the state of the interpreted version and starting
the compiled version from a fresh slate.

Dynamic deoptimization, as in Self, is just the opposite: it requires
transforming the current state of the machine-code program into a
corresponding state of the source-code program so the programmer can
debug it.

Error handling
--------------

This seems to have been Hopwood’s primary concern in the design of Noether.

With a transaction per subroutine invocation,
error handling becomes substantially easier.  Nonlocal exceptions are
especially popular in pure functional languages because cleanup while
unwinding the stack is unnecessary; by contrast, C++ had so much trouble with this
that the STL wasn’t exception-safe for several years after it was written!  In fact, if I
understand correctly, exceptions are still prohibited at Google,
because they complicate reasoning about what happens in failure
cases — precisely what kinds of states can result.  But in such a
transactional system, the transaction system takes care of cleaning up
any incompletely made changes.  So you don’t need RAII, destructors,
or special failure handling.

The basic nested-transaction feature doesn’t require tracking *reads*
of transactional variables, the way Haskell’s STM does, only *writes*.
That’s because there’s no need to check a transaction for validity
when you go to commit it — no other code could have been running
concurrently.  You only need to buffer the writes to transactional
variables so that you can undo them if you have to roll back.  (This
is a general property of pessimistic synchronization, and this is just
the extreme case of it, as explained later.)

Fearless concurrency
--------------------

As Hopwood points out in zir 2014 Strange Loop presentation, logging
writes in this way is also what you need for a concurrent or
generational garbage collector.

However, if you do additionally track reads of transactional
variables, you can use the transaction system for multithreading with
a guarantee of serializability.  This is probably costly unless
the language is mostly functional, like Clojure or OCaml, and only
slightly imperative, because pervasive Python-style mutability would
entail logging a huge amount of read traffic to the mutable variables,
similar to the overhead of unoptimized reference counting.  The
per-call transactions would reduce the cost of retrying in most cases.

There’s the question of when the threads of such a multithreaded
program would *not* be in a transaction, making their transactional
mutations visible to other threads.  I think the answer is something
like Erlang’s top-level process loop, where the process evolves by
having its top-level procedure make a tail call to itself, and of
course when a thread exits successfully.

Such a system would be sort of like the “dynamic typing” equivalent of
Rust’s fearless concurrency through lifetime checking: your program’s
non-interference is checked dynamically at run-time, and corrected if
necessary, rather than proven at compile-time.  But there is a crucial
difference: unlike dynamic type checking, it’s not just turning a
subtle failure into an easier-to-understand failure; it actually
*removes the bug*, thus dramatically simplifying the correct code by
factoring the hairy concurrency questions out of the application.  So,
while dynamic typing typically makes code harder to statically prove
correct, this kind of dynamic concurrency checking should make code
*easier* to statically prove correct.

Optimistic vs. pessimistic concurrency defined
----------------------------------------------

(This section is not specific to nested transaction systems,
transactional memory systems, or even indeed to transactional systems
at all; it applies to all forms of concurrency in software.)

“Optimistic concurrency” means that things don’t block each other;
instead you allow transactions to run to completion, and if there’s a
conflict, the first one to commit wins.  This guarantees progress and
liveness at the potential expense of machine efficiency.  “Pessimistic
concurrency” is where you use locks to ensure that you don’t waste any
work on transactions that would have to be rolled back due to write
conflicts.  Most systems use a mixture rather than purely one or the
other.

Pessimistic concurrency is helpful, for example, for interoperating
with systems outside the scope of the transactional system, because
transactions only roll back (and possibly have to be retried) if they
are buggy and try to commit something erroneous.  This way, the
transaction system avoids imposing any obligation of rollback on such
external systems, and the transaction system itself only needs to
support rollback for error recovery.

In general, doing pessimistic concurrency safely requires some kind of
static analysis of your transaction code to find out what resources it
*could possibly* read or write, so that it won’t be started until it
can acquire all of them.  (This lock acquisition can be atomic, but
it’s sufficient for it to happen in a deterministic order in every
transaction to prevent deadlocks; and doing some computation in
between lock acquisitions is actually okay.) To be computable, this
analysis must be conservative, so in case of doubt, it will delay your
transaction until it can guarantee that it will be able to succeed.
In the limit, pessimistic concurrency reduces to no concurrency:
acquiring a global system lock, as in Noether and other traditional
event-loop systems like Monte, Tcl/Tk, Twisted Python, asyncore, or
JS.

This kind of static analysis is generally infeasible (for the
transactional system to do, at least) in the context where pessimistic
concurrency is most appealing: that of interoperation with external
non-transactional systems, or systems that otherwise cannot fulfill a
commitment to roll back changes.  So pessimistic concurrency tends to
suffer deadlocks from time to time, even though this is theoretically
avoidable.

Aside from the deadlock issue, pessimistic concurrency suffers from an
efficiency problem in the multicore era (which, for transaction
systems, began with VAXclusters).  If your limiting resource is CPU
cycles, then to guarantee *efficient* progress, then *pessimistic*
synchronization is the ticket: if a transaction read-locks every
mutable variable it reads and write-locks every mutable varible it
writes, then you never have to retry anything, so then the only way
you can go slower than maximum speed is if you have deadlock or run
out of work.  And this is important — a system making sufficiently
slow progress is effectively indistinguishable from a deadlocked
system, as anyone will attest after trying to use a desktop Linux
system that’s thrashing in swap.

However, by never burning a CPU cycle it can’t prove will get
committed, pessimistic synchronization fails to take advantage of
available CPU resources in uncertain situations, thus conserving
energy at the expense of speed.

Both forms of synchronization suffer from low throughput in situations
of high contention, and both can get high throughput in situations
where non-contention can be detected.  So in both cases the best way
to get high concurrency is to keep your transactions short.  But
optimistic synchronization resolves contention with a strong bias in
favor of short transactions, while pessimistic synchronization
resolves contention with a strong bias in favor of long transactions;
it’s easy to get into a situation where your
pessimistically-synchronized 1000-transaction-per-second system is
processing 1 transaction for 30 minutes.

Modular blocking
----------------

You might think that this approach would preclude I/O anywhere but at
some sort of top-level event loop, at least per thread, since I/O is a
side effect.  It’s straightforward to see how you could buffer up
output (maybe logging it for debugging in case of an abort) until the
top level is reached, but how could you do that for input?

Fortunately [_Composable Memory Transactions_][1] has a solution to taking
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

[1]: https://www.microsoft.com/en-us/research/publication/composable-memory-transactions/

This provides, in the words of the paper, “a modular form of
blocking” — a thread can wait on a condition variable, or an arbitrary
Boolean function of various transactional variables, or anything else
that can be shoehorned into the transaction system, including input
events — and the functions that do such waiting can be made
nonblocking by having a fallback that always succeeds, or combined by
falling back from one to the other.

As Shae Erisson points out, this could integrate well with modern
event-driven I/O systems like Linux’s `io_uring`: a thread reading the
event source can enqueue events in internal queues, thus inducing
other transactions to get retried.

Safe aborting for guaranteed responsiveness
-------------------------------------------

Another benefit provided by pervasive transactionality — and this one
wouldn’t require either read-logging or nested transactions — is that
any task can always be safely aborted, which eliminates the Sophie’s
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

This same sort of write-log consultation is also needed for
concurrency with optimistic synchronization: if some other transaction
might be concurrently reading the home location of a transactional
variable, it needs to see the previous committed state, not the state
that might possibly be committed.  (This could be done by instead
having all reads of mutable variables check all active undo logs for
old values, but that is even worse.)  Pessimistic synchronization is a
way to avoid this.

This possibility of abandonment through rollback solves one of the
knottiest problems in E-style event-loop object-capability systems
such as Monte: in a vat shared between code from mutually untrusting
security domains, it is always possible for one security domain to
deny service to the other by running an infinite loop.  By providing a
guaranteed safe way to abort and retry event handlers, such
abandonment eliminates this risk, thus enabling closer and more
efficient cross-domain collaboration.  (However, you still have to do
most of the communication between the domains with eventual sends to
get this nonblocking benefit, so it may not be more convenient.)

With virtual memory, one common problem for responsiveness is that
when the system starts to thrash, responsiveness for the whole user
interface goes to hell, because there’s no reasonable way to make
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

These approaches could even guarantee hard-real-time event response.
Hardware interrupts, or software interrupts such as Unix signals, can
be handled in this way.  If such hard-real-time tasks are to have
strictly bounded response times, though, we must render it impossible
for other tasks to delay their progress.  On a single-threaded
computer this is easy — just don’t run any other code until the
interrupt handler completes.  On a multithreaded computer, such as one
with multiple processors or multiple hardware threads, it is necessary
to use some kind of pessimistic synchronization to prevent any other
top-level transaction from committing that could require the interrupt
handler task to rollback and retry — this also makes it safe for the
interrupt handler to manipulate the outside world without waiting for
its transaction to commit first.

Support for optimistic synchronization and running the interrupt
handler as a top-level transaction is all that’s necessary to get it
to *start* running promptly, and then blocking any possibly
interfering concurrent transactions (and any other interrupts) is all
that’s needed to ensure that it can *finish* running promptly without
any retries.  When the interrupt handler finishes, the changes it
commits may or may not cause other transactions (blocked or not) to
have to retry.  So it isn’t always even necessary to *discard* the
work in progress to guarantee responsiveness to urgent events in this
way.  But buffering the writes of uncommitted transactions in a write
buffer, rather than logging an undo-log record and updating mutable
variables at their home locations, seems to be necessary for
optimistic synchronization, and sufficient for constant-time work
abandonment.

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

These error values can propagate along the program’s dataflow graph,
like floating-point quiet NaNs; they only leap over to the
control-flow graph if they are “leaked” or “dropped”.

XXX add example

Modal reasoning
---------------

Another application of transaction rollback is *code search*, as
suggested by Hopwood in hir 2014 talk under the heading “confining
side effects”, based on Joel Galenson’s† [CodeHint] (which cites the
Squeak method finder): is there an existing function in my code base
that will convert 4 and 66 into “iv” and “lxvi” respectively?  How
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
transfers and is precise, it’s safe, but not if it’s counting
wall-clock time or clock cycles and/or is checked only irregularly.)

A generalization of this is the ability for a program to reason about
code’s behavior under conditions that do not presently prevail, simply
by running it inside a transaction that is then rolled back.  This
does require the transaction’s rollback notification to contain enough
information to tell us what we want to know about the code’s behavior,
but that’s probably a requirement for useful transaction failure
messages, anyway.

[CodeHint]: http://people.eecs.berkeley.edu/~bjoern/papers/galenson-codehint-icse2014.pdf

Given this kind of facility, you could reasonably ask questions such a
the following: Which methods would write to some field of this object?
Is there any live object on which calling the “.open()” method would
read the current user ID?  What is the object whose “.destroy()”
method would return the highest value?

In the debugger context, this kind of automatic cleanup would allow
you to view “speculative” executions as well: the hypothetical flow of
values through a piece of code, without the risk of corrupting the
“true” state of the program under inspection with a side effect.

† and Philip Reames’s, and Rastislav Bodik’s, and Björn Hartmann’s,
and Koushik Sen’s CodeHint

Memoization and incrementalization
----------------------------------

Suppose the transaction for a procedure invocation
is logging all its reads and writes of mutable data; if it
additionally logs which procedure it is, any closed-over data, and its
input parameters, then it becomes possible to use it for
memoization — any call to the same procedure with the same parameters
and closure data will necessarily perform the same writes and return
the same value, unless either one of those reads is out of date or execution
is nondeterministic.  So it’s
valid to just perform those writes and return those results without
actually running any of the function’s code.  This is very similar to
a build system like `make`, or to Umut Acar’s “Self-Adjusting
Computation”; it provides a way to transparently incrementalize a
computation, so that it can be efficiently re-executed on slightly
modified input.  Also, it automatically derives a
guaranteed-linear-time Packrat parser from an ordinary
exponential-time recursive-descent parser.

Moreover, this caching or memoization is still valid *even if the
original memoized computation was a child of a transaction that was
rolled back*.  That is, even computation that was “discarded” can
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
   “invertible” in practice (in the sense that you can in practice
   find an input that produces a desired output, not in the sense that
   such an input exists or is unique),
   permitting the practical solution of a wide
   variety of inverse problems.  The optimization procedure can
   randomly alter the program’s input, propagating the incremental
   changes through the incrementalized program, in order to converge
   on the desired result.

3. A special case of the former is generative software testing like
   that done by Hypothesis or American Fuzzy Lop, where the “desired”
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
   shades into the “code search” application mentioned earlier.

In short, incrementalization reduces the need for explicit caching and
makes searching over the space of executions immensely more efficient.

As an example of “invertibility in practice”,
or “solving inverse problems”, you could imagine
applying a ray tracer like [Peter Stefek’s incremental ray tracer][5]
to solve photogrammetry or caustic design: by searching for an input
3-dimensional scene that closely approximates a movie taken by a
moving camera, you can estimate the geometry of a scene.  [Mitsuba
2][6], for example, has demonstrated this using automatic
differentiation rather than incrementalization.  (As I said in
Dercuano, I suspect that integrating reduced affine arithmetic into
the caching system might make it possible to do this trick much more
effectively by permitting limited errors in the output, so that memo
table values can be reused even for slightly changed inputs.)

[5]: https://www.peterstefek.me/incr-ray-tracer.html
[6]: http://rgl.epfl.ch/publications/NimierDavidVicini2019Mitsuba2

Integrity enforcement
---------------------

Hopwood also describes the use of such write logging to help with
invariant maintenance: the write log tells you which objects have been
changed in a transaction and whose state thus ought to be checked for
correctness, and transaction rollback gives you the wherewithal to
undo the damage.  This is of course precisely the “C” in “ACID” in the
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

Zelkowitz’s work in 1971 found that adding comprehensive undo logs to
PL/I only added about 70% execution time to his PL/I programs (and
bloated the programs themselves somewhat); he didn’t report on runtime
memory usage, which I’d think would often be the more crucial aspect.

Even with read logging and competing with modern compilers, the cost
for one transaction per subroutine invocation for a pervasively
mutable language like Python might be comparable to CPython’s existing
interpretation cost.  But for many purposes CPython’s performance is
unsatisfactory.

### Reducing the number of mutable variables ###

As mentioned above in the “fearless concurrency” section, the cost of
logging reads and writes ought to be proportionally lower in a
language design with many fewer mutable variables, like Haskell,
OCaml, or Clojure — though, by the same token, many of the potential
benefits are smaller: for debugging you’ll need to use a smart diff
for path-copied data structures or other FP-persistent data
structures, tail-call looping constructs already provide on-stack
replacement, cleanup from exceptions is rarely necessary, and in many
cases it’s possible to confine bits of code for safe experimentation
(for modal reasoning or debugging) using mechanisms the type system or
object-capability discipline without resorting to transactions.  The
benefits for concurrency, I/O composability, and responsiveness remain
unchanged, but they pertain to transactional-memory systems in
general, not just those with implicit per-invocation nested
transactions.

Even in pure or very-nearly-pure functional programming systems,
acquiring the benefits of the time-limiting, automatic memoization,
and incrementalization features described above requires other kinds
of work, such as hash consing and the development of good cache
eviction heuristics.  This work is needed with or without
transactions, and promises to be the lion’s share of the job.

### Aggregation ###

Aggregation is another common way to reduce the cost of read barriers,
write barriers, and dependency tracking for incremental computation
and rollback.  The idea is that, by agglomerating mutable variables
into larger units, we can reduce the work needed to track them.

Under `make`, compilers and linkers communicate through the
filesystem; if the compiler† changes `psmouse.o`, `make` reinvokes the
whole linker with the whole new `psmouse.o`.  It doesn’t care which
parts of `psmouse.o` have changed, and its devil-may-care attitude
buys much less dependency-tracking overhead on the compiler’s workings
in exchange for a less precise incremental recompilation, involving a
full relink.

If we try to analyze the `make` example in functional-programming
terms, we could say that the compiler mutates the `psmouse.o` entry in
a mutable directory to point to a new (immutable) binary string — the
new contents of the object file; or that the compiler produces a new
state of the filesystem in which the directory is a copy of the old
directory except with `psmouse.o` pointing to different contents, and
that is in fact more or less how Git implements directories in
commits.  (Even if you wouldn’t normally commit `psmouse.o`.)  But
another way to analyze it is that the compiler applies a sequence of
mutation operations to `psmouse.o`: first truncating it, then
appending various blocks of bytes, perhaps even seeking around and
backpatching some bytes.  What strace shows is somewhere in between:

    [pid 26311] stat("psmouse.o", {st_mode=S_IFREG|0644, st_size=2352, ...}) = 0
    [pid 26311] lstat("psmouse.o", {st_mode=S_IFREG|0644, st_size=2352, ...}) = 0
    [pid 26311] unlink("psmouse.o")         = 0
    [pid 26311] open("psmouse.o", O_RDWR|O_CREAT|O_TRUNC, 0666) = 3
    [pid 26311] write(3, "\0psmouse.c\0main\0read\0printf\0putc"..., 53) = 53
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 4096) = 1029
    [pid 26311] lseek(3, -965, SEEK_CUR)    = 64
    [pid 26311] write(3, "UH\211\345H\203\3540dH\213\4%(\0\0\0H\211E\3701\300\307E\320\0\0\0\0\307E"..., 516) = 516
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 4096) = 1029
    [pid 26311] lseek(3, -445, SEEK_CUR)    = 584
    [pid 26311] write(3, "\24\0\0\0\0\0\0\0\1zR\0\1x\20\1\33\f\7\10\220\1\0\0\34\0\0\0\34\0\0\0"..., 56) = 56
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 4096) = 1029
    [pid 26311] lseek(3, 3, SEEK_CUR)       = 1032
    [pid 26311] write(3, "7\0\0\0\0\0\0\0\2\0\0\0\n\0\0\0\374\377\377\377\377\377\377\377g\0\0\0\0\0\0\0"..., 384) = 384
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 4096) = 1416
    [pid 26311] lseek(3, -776, SEEK_CUR)    = 640
    [pid 26311] write(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\4\0\361\377"..., 336) = 336
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 4096) = 1416
    [pid 26311] write(3, "\0.symtab\0.strtab\0.shstrtab\0.rela"..., 97) = 97
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] write(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\1\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 64) = 64
    [pid 26311] lseek(3, 0, SEEK_SET)       = 0
    [pid 26311] read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\1\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 4096) = 1513
    [pid 26311] lseek(3, 7, SEEK_CUR)       = 1520
    [pid 26311] write(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
    [pid 26311] close(3)                    = 0

From the point of view of the kernel, the compiler† is mutating
`psmouse.o` nine or ten times: first it unlinks the old file, then it
creates the new one (`O_TRUNC`ing it if it somehow already exists),
and then it write()s into it eight times at six different offsets.

But `make` doesn’t care about that level of detail; it’s content to
work with the knowledge that `psmouse.o` has changed.  So, for
transactional purposes, it’s unnecessary to keep noting that
`psmouse.o` keeps changing unless we’re creating new rollback points;
it’s adequate to keep a snapshot of its previous state.

Similarly, if we have a large numerical array we’re running a mutation
loop over, it’s adequate for many purposes to snapshot the whole array
before the first mutation, rather than tracking individual mutations
on the array.  Analogously, array-computation libraries with automatic
differentiation like TensorFlow track computational dependencies
between entire arrays (vectors, matrices, etc.) rather than individual
scalars within them.

The card-marking write barrier developed for Self used a single dirty
bit for each chunk of memory (of I think 32 or 64 bytes), keeping the
write-barrier data tiny and the write-barrier code fast, at the
expense of imposing extra scanning work on the garbage collector.
Similarly, for purposes of swapping out individual objects to disk,
LOOM (Kaehler & Krasner 1982) used a single dirty bit per Smalltalk
object.  Logical logging for transactional RDBMS rollback logs
typically stores [before-images of rows being updated][7] rather than
the individual updated fields, and [physical logging, used for rapid
recovery to checkpoints rather than rolling back individual
transactions, instead stores before-images of entire pages][8].
(Terminology varies somewhat between databases.)

[7]: https://www.ibm.com/support/knowledgecenter/SSGU8G_11.50.0/com.ibm.admin.doc/ids_admin_0694.htm
[8]: http://www.informix-dba.com/2010/07/blogging-about-logging-informix.html

And, of course, virtual-memory operating systems typically track
memory dirtiness at the granularity of a hardware page — 512 bytes on
the VAX and 4096 bytes on most other systems — and handle
copy-on-write data at the same granularity.  Traditional FORTHs do the
same thing, but with 1024-byte blocks.

For our transactional purposes we’d need to do more than set a dirty
bit; as with RDBMS logging, we’d need to copy the clean data before
modifying it, either into an undo log (as in Noether) or into a buffer
of pending writes for the current transaction (to permit optimistic
synchronization or constant-time rollback).

In [the note on segments and blocks](segments-and-blocks.md) I
outlined a virtual-machine system in which the virtual machine has a
number of “descriptor registers” which mediate its access to memory,
which consists of “segments” and “blocks”; read and write access is
checked when a new descriptor is loaded into a descriptor register,
while any number of accesses via an already-loaded descriptor can
proceed with no further checking.  Loading a read/write descriptor
register would potentially trigger a copy of the segment or block to
be modified.  This is explained in more detail in that note.

†Typically the assembler on Unix, actually.

### Advancing top-of-stack rollback points rather than adding new ones ###

If we’re *only* using transactions for error recovery and/or
peremptory work discarding for responsiveness (not memoization,
multithreading with optimistic synchronization, deoptimization, or
debugging, as suggested above), then, when a parent procedure invokes
a child procedure at a callsite where failures in the child will
necessarily propagate to a failure in the parent, it’s not necessary
(for execution) to preserve the separate transaction for the child
procedure — if the child rolls back, the parent rolls back too.  This
optimization dramatically reduces the amount of extra work imposed by
the transaction system, and in particular something like it is
mandatory for systems like Scheme that rely on tail-call optimization
for looping.

### Local variables and escape analysis ###

A subroutine can mutate its local variables freely without incurring
any transaction overhead, unless those variables are referenceable
(something impossible in, for example, Scheme) and references to them
have in fact escaped.  For example, Pascal-style `var` parameters can
enable references to local variables to be passed to callees, but the
language guarantees that once the callees return, those references are
no longer live.

Plumbing transactions to the user interface, the filesystem, and the network
----------------------------------------------------------------------------

Depending on what filesystem you’re running and how deeply you’ve been
hurt, you might be able to trust the filesystem to honor your
transaction boundaries as well, which means that code inside a
transaction can read and write the filesystem freely — but the
filesystem must give us a way to keep the writes within a
transactional bubble, hidden from the rest of the world at first, and
perhaps forever.  Also, it must give us a way to transactionally
validate our reads when we go to commit, if there’s a possibility the
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

If you’re willing to embrace the filesystem and networked services as
part of your transactions, what about users?  In particular, if you
can run multiple entire programs inside a giant transaction, you could
enable users to create a long-lived transaction that they then have a
window into, as a way to experiment with new states they may not want
to keep.  However, I’m not sure this approach can really deliver a
usable user experience of undo and restoration from backups; NixOS has
its fans, in part because it offers a much freer model of switching
between configurations than simple nested commit/rollback.
On the other hand,
using this approach for debugging implies that it’s possible for users
to see inside an uncommitted transaction, at least within the
debugger; being able to can copy things out of the transaction history
or an uncommitted transaction might be enough.

Is there a connection with hardware transactional memory support that
is starting to appear in modern high-end manycore systems?  It is in
some ways a way to expose the multisocket nature of the system to
application software so that it can avoid paying unnecessary
synchronization costs.  How would it play with this kind of
per-subroutine-call nested transactions?

Reverse-mode automatic differentiation
--------------------------------------

Implementing this kind of rollback suffers from the same difficulties
as reverse-mode automatic differentiation, namely that it needs to
keep around all the intermediate values that have been overwritten, or
anyway those that were live at a live rollback point.  The checkpoints
it provides could in fact literally be used as the checkpoints for
reverse-mode automatic differentiation, a further crucial technique
for solving inverse problems.

First-class transactions
------------------------

What would it look like to, as Shae Erisson suggested, expose the
per-call transactions as first-class objects to the user program?  You
could imagine, for example, inspecting your current transaction to see
what mutable variables it had read or written, or the rolled-back
transaction executed by a callee, and this would provide a natural
interface for applying the technique to the various problems described
above.

Thanks
------

Thanks to sbp, Darius Bacon, Corbin Simpson, and especially Shae Erisson for
many very informative discussions that helped greatly with this note.
