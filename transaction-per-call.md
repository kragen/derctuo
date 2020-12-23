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
the stack so far.

And implementing edit-and-continue in the debugger
becomes substantially easier under some circumstances when you can
restart the function you’ve just edited.

Also, being able to see
which transactional variables are being *depended on* at each level in
the call stack is also a potential boon to debugging, sort of like
`strace` at a per-function level.  This could even permit you to produce
an interactively explorable dataflow digraph of the call tree; in a
standard bubble-and-arrow diagram, dataflow edges might be displayed
as connecting to the lowest visible ancestors in the call tree,
which you could interactively explode into self and callees.  Other
forms of aggregation for debugging (grouping together all calls to a
particular procedure, or all calls from a particular callsite) might
also be insightful.

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
debug it.  This is closely related to the time-travel feature
described in the previous section.

Error handling
--------------

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

This seems to have been Hopwood’s primary concern in the design of Noether.

Fearless concurrency and distribution
-------------------------------------

As Hopwood points out in hir 2014 Strange Loop presentation, logging
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

A significant feature of this kind of concurrency is that it can be
nested and physically distributed over a parallel virtual machine: a
“master server” node might own the “home location” of all global
variables, while a “pool worker” node might (in the optimistic-sync
case) start a top-level transaction that reads them from time to time
and then in the end sends a commit message listing all the variables
it read and all the variables it’s writing, which the master can
accept or abort.  Meanwhile, the pool worker can create non-global
transactional variables that exist only inside its transaction, and
farm out work to subcontractor subtransactions potentially running on
other subcontractor nodes, proxying their reads of transactional
variables through the parent transaction’s node.

(To avoid ABA problems, probably a monotonically increasing revision
number for each transactional variable depended on should be in the
commit message, rather than just the value the variable happened to
have at the time.)

Worker nodes can maintain a local cache of cached values for global
mutable variables.  It’s okay if the items in the cache get outdated,
because the master will reject the commit message for any transaction
that has read an outdated value from such a cache — all that’s lost is
the CPU time wasted doing work that now must be retried.  The system
would still work properly, though inefficiently, if such rejected
commits were the only way to learn about outdated cached values, but a
more efficient way for a wide variety of scenarios is to implicitly
add an observer to the variable when processing a read-variable
message, such that a single cache invalidation notification will be
sent to the reader when the previously-read value has been updated, so
the reader can invalidate their cache.  Since this is an optimization,
it’s okay if the invalidation messages aren’t reliable, but for most
usage scenarios it’s best to discard the observer relationship after
sending the invalidation message, so at most one invalidation packet
(and one current-value packet) is sent per read packet.

The way that works out differs depending on the access pattern.
Global variables that are frequently read and almost never updated are
almost always globally cached; after each update, the master sends out
invalidation messages to nearly all workers, which respond by retrying
a lot of in-progress transactions, which immediately send read
messages to get the new values of the variable, so it’s effectively a
two-packet-per-node broadcast of the new value.  Global variables that
are frequently written and almost never read are also almost never
cached, so each write produces almost no invalidation traffic.  Global
variables that are frequently written and also frequently read
unavoidably produce a lot of traffic and also a lot of retried
transactions, unless some sort of pessimistic synchronization is used,
in which case they instead produce inefficient serialization.

The cases where this caching/invalidation mechanism is insufficient
are the extreme cases where either it results in an unacceptable waste
of CPU time in transactions that will abort, where it’s unacceptable
to have to wait for a cache miss to be served from the master server,
or where sending a separate copy of a new value of a popular or
voluminous variable to every client is unacceptable.  The first case
can be handled with pessimistic synchronization (see below) while the
other two cases can work by supplementing the usual cache-invalidation
mechanism with a “push” mechanism that immediately broadcasts new
values of popular variables before anyone asks for them, for example
using Ethernet multicast.

This scheme also permits proxies which pass through your global
transactions to the real master server (or master server cluster),
which look just like a real worker to the master server and just like
a real master server to the real workers.  The proxies answer almost
all variable-read queries from their caches, without bothering the
real master server, and when they receive a transaction commit
message, they simply forward it on to the real master, then relay the
COMMITTED or ABORTED response to the real worker.  This is analogous
to the scenario described above with a worker node farming out jobs in
subtransactions to subcontractors.  By this means it is possible to
scale horizontally in the same way people do with MySQL readslaves.

These proxies, again like a parent transaction, can run consistency
validation code on the state of the database after a transaction,
aborting the transaction if the consistency checks fail.  This is
related to the “integrity enforcement” section below.

Sharding the database of global mutable variables across multiple
master servers is somewhat problematic, because each transaction needs
to commit or abort atomically.  The standard consensus protocols for
distributed transactions (two-phase commit, three-phase commit, Paxos,
Raft, Chandra–Toueg, Mostefaoui–Raynal, ZooKeeper ZAB, in some cases
Nakamoto consensus) can be used.  For some cases, you could instead
add new “global” mutable variables belonging to a proxy described
above, which are visible to everyone sharing the same proxy, in the
same way that mutable variables created within a transaction and not
exported are visible to subtransactions.

So, as with the single-machine version of the system, it’s important
to limit the number of writes to global mutable variables, and in
particular contention on them.  To the extent that you can instead
pass around immutable data structures, for example blobs identified by
their BLAKE3 hash, you can reduce the work centralized in the master
server.  Note that this doesn’t necessarily mean you want to minimize
the *number of variables* that are global and mutable; if you’re
building a distributed filesystem this way, for example, you could get
by with a single global mutable variable for the root of the
filesystem (like how a Git HEAD refers to a commit by hash), but every
write to the filesystem would invalidate it and force all existing
transactions to be restarted.  Instead you would probably want at
least one mutable variable per file, possibly one per data block, to
prevent concurrent transactions from conflicting, even at the expense
of increasing the load on the master.

REST and the continuation-based web frameworks exemplified by Paul
Graham’s Arc and the Smalltalk system Seaside can integrate with such
systems in an interesting way.  Consider a web server serving up an
HTML `<form>` for changing a field in a database record.  If the
`<form>` contains a hidden “manifest” field that lists all the
transactional variables read to produce the page, along with the
relevant values of their version counters, then when the form is
submitted, the submit handler can check all of these variables to see
if they are outdated, and in such a case produce an error page for the
user, thus preventing lost-update conflicts where the user’s desired
change no longer makes sense in light of something else on the page.
However, in practice you’d probably want to limit the scope of these
dependencies, so that a change to something unrelated (the number of
users currently online, the current time) doesn’t produce spurious
errors.

This “manifest” mechanism, in a sense, permits the protection of
(purely optimistic) transactions to be extended all the way out to
untrusted browsers, either with no server-side session state in full
compliance with the REST model, or by storing the session state in a
time-limited variable on the server identified by a continuation ID.

In summary, transactions, especially per-call transactions, enable the
single-system-image programming model to be extended with acceptable
efficiency across a distributed network of up to a few thousand nodes,
including to some extent mutually untrusting actors, unreliable
networks, unreliable nodes, heterogeneous software and protocols, high
latency, though with a single root of trust (“there is one
administrator,” in Peter Deutsch’s phrase).  They would do so by
hiding latency with concurrency, avoiding latency and reducing
bandwidth with safe caching including proxies, recovering from
failures, and automatically retrying transactions safely after node or
network failures.

Optimistic vs. pessimistic synchronization defined
--------------------------------------------------

(This section is not specific to nested transaction systems,
transactional memory systems, or even indeed to transactional systems
at all; it applies to all forms of synchronization in software.)

“Optimistic synchronization” means that things don’t block each other;
instead you allow transactions to run to completion, and if there’s a
conflict, the first one to commit wins.  This guarantees progress and
liveness at the potential expense of machine efficiency.  “Pessimistic
synchronization” is where you use locks to ensure that you don’t waste any
work on transactions that would have to be rolled back due to write
conflicts.  Most systems use a mixture rather than purely one or the
other.

Pessimistic synchronization is helpful, for example, for interoperating
with systems outside the scope of the transactional system, because
transactions only roll back (and possibly have to be retried) if they
are buggy and try to commit something erroneous.  This way, the
transaction system avoids imposing any obligation of rollback on such
external systems, and the transaction system itself only needs to
support rollback for error recovery.

In general, doing pessimistic synchronization safely requires some kind of
static analysis of your transaction code to find out what resources it
*could possibly* read or write, so that it won’t be started until it
can acquire all of them.  (This lock acquisition can be atomic, but
it’s sufficient for it to happen in a deterministic order in every
transaction to prevent deadlocks; and doing some computation in
between lock acquisitions is actually okay.) To be computable, this
analysis must be conservative, so in case of doubt, it will delay your
transaction until it can guarantee that it will be able to succeed.
In the limit, pessimistic synchronization reduces to no synchronization:
acquiring a global system lock, as in Noether and other traditional
event-loop systems like Monte, Tcl/Tk, Twisted Python, asyncore, or
JS.

This kind of static analysis is generally infeasible (for the
transactional system to do, at least) in the context where pessimistic
synchronization is most appealing: that of interoperation with external
non-transactional systems, or systems that otherwise cannot fulfill a
commitment to roll back changes.  So pessimistic synchronization tends to
suffer deadlocks from time to time, even though this is theoretically
avoidable.

Aside from the deadlock issue, pessimistic synchronization suffers from an
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

One interesting compromise is granting a limited-time lease on a
variable, which prevents any other transaction from altering it during
that time.  If your transaction commits while holding the lease, you
are guaranteed that nobody has written to the variable in the
meantime, so if your transaction has to roll back and retry, at least
it won’t be because of *that* variable.  If it commits while holding
such leases on all variables it read, it is guaranteed to not have to
retry because of any of them.  Similarly, you can grant “write-leases”
or “write options” (“put options”?), which prevent anyone from taking
out a read-lease on the variable during the given time.  So if your
transaction has an unexpired read lease on every variable it read, and
an unexpired write lease on every variable it wrote, it is guaranteed
to be able to commit without retrying.  In a distributed system that
can tolerate node failures, this is the only kind of lock that can
ever be granted; otherwise an unreachable node could hold locks
forever, blocking some and perhaps eventually all transactions in the
system.

The transaction manager doesn’t necessarily have to tell the
transactions that it’s granting them a lease, and if it does, it can
choose the expiry date at will.  Leases can be purely an optimization
to improve throughput in the face of heavy contention by reducing the
fraction of CPU wasted on doomed transactions.

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

I’m not quite sure how precisely we can compute “any possibly
interfering concurrent transactions” or whether this benefits from
static analysis of the interrupt handler.  Clearly if another
(top-level) transaction tries to write to a variable the interrupt
handler has read, it needs to be at least blocked from committing
until after the interrupt handler.

Specifically with respect to screen updates, it would be useful to
break up the screen repaint into three pieces: a top-half “push” that
runs as part of input processing, which takes a small, bounded amount
of time to ensure high input handling throughput to recover from
overload conditions; a “pull” that runs as part of the vertical
blanking interrupt or even the *horizontal* blanking interrupt, which
is higher priority than input processing and also takes a bounded
amount of time, and whose reason for being is to allow the top-half
push to do less work by using a more efficiently updatable in-memory
representation (a scenegraph, a display list, a set of sprite
positions, a tilemap, etc., of some bounded complexity; see the notes
in [Scribal Basic](scribal-basic.md) about the Atari 800); and a
bottom-half push that is scheduled after input processing, can be
abandoned and restarted if new input comes in, and can take unbounded
time to more elaborately update the structures read by the pull
transaction.  For example, the bottom-half push might read in text
from a disk file after it’s newly scrolled into view, or overwrite an
approximate 3-D rendering with a more precise one, possibly more than
once in multiple different transactions.

In an OLTP database context, you could imagine handling incoming write
transactions (“writes”, including record updates, insertions,
deletions, and schema changes) by appending them to a journal and
scheduling additional transactions to update views and indices
(“rebuilds”, though presumably incremental).  Read transactions
(“queries”) that consulted a view or index would also need to read
through whatever part of the journal was not yet accounted for by the
view or index in question.  An OLTP workload usually won’t work with a
hard priority system, since totally starving any of writes, rebuilds,
or queries due to a high load of the other two would be unacceptable;
the relative priorities of writes, queries, and rebuilds could be
adjusted through an internal pricing system, in which writes and
queries earn “money” by spending CPU time and perhaps IOPS, writes are
additionally billed for the expected losses from slower queries, and
rebuilds earn “money” by reducing the expected costs of queries, which
is at least in part an option value — Black–Scholes may be the right
valuation.

A partly completed agoric OLTP transaction would tend to be able to
bid higher for resources than one that hadn’t started — if its
expected completion time is 2 ms and doesn’t change during evaluation,
and its expected net earnings are 2 simoleons, it can initially bid
1000 simoleons per second, but after running for 1.5 ms, it can bid
4000.  But, if that’s not high enough because another job with much
higher value has arrived, it’s “socially optimal” to abort the
currently running transaction and handle the higher-value job.

(This same OLTP approach also applies, of course, to updating source
code and computing executable views of it with a compiler or groveling
over the update log with an interpreter; this could entirely eliminate
the JIT pause problem that plagued Self, if Moore’s Law hadn’t already
taken care of that.  Yet people still sometimes wait for rebuilds to
finish.)

To support simultaneous OLAP operations on the OLTP database, you
could simply run your queries on the most recent available indices and
views, including precomputed rollup views, without taking the
still-unincorporated journals into account.

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

Above I talked about using transaction scheduling as a way to
guarantee responsivity for real-time and OLTP systems, in particular
allowing updating of indices and views to be deferred to some degree
to improve query responsivity.  A simpler, though probably lower
performance, design is to compute an index (or a view) as the cached
result of a giant computation over one or more entire tables, or even
the update log.  Then, queries that consult this index will first
request the index in a cached subtransaction, made out of smaller
subtransactions; normally this will be instant, served from the memo
cache, but in other cases will require a partial or full recomputation
to bring the index up to date.

So, for example, you might have 99000 data blocks in an append-only
table, each containing 10 rows.  Each data block is an immutable blob
pointed to by a separate mutable variable, and there’s another mutable
variable that’s a list of all 99000 blocks.  Every append to the table
appends a row to the last data block (by copying the other 9 or less
rows into a new block), or if it’s full, creates a new mutable
variable, points it to a block of one row, and adds it to the list.
The index on column FOO is an LSM-tree, consisting of a run of the
sorted FOO values (and record numbers) of the first 65536 rows in the
update log, a run of 32768 FOO values, a run of 512 FOO values, a run
of 128 FOO values, and so on for 32, 16, and 8.  So when a new row is
added, maybe a new run gets added, or normally the smallest few runs
get jiggered around a bit in the next query, but the 65536-item run
and the 32768-item run are returned immediately from the cache rather
than being recomputed.

This scheme “works” with tables that are being updated “in place” (by
replacing immutable data blocks at random offsets by slightly
different immutable data blocks) in the sense that queries will never
return the wrong answer, but suppose someone updates record 50000.
This will invalidate, among other things, one of the leaves under the
65536-item run in the index; if the FOO value has changed, this change
will bubble up to recomputing that 65536-item run by merging together
two 32768-item runs, the first of which is hopefully still in the
cache despite not having been used in quite a while.  This takes some
65536 comparisons, which is not a lot of work in an absolute sense but
still about four orders of magnitude larger than what you would hope
to see for a single record update.  Also when you append record 131072
you are going to have to do 131072 comparisons the next time you run a
query that uses that index.

I think you can repair this approach to some degree by storing the
table as a segmented journal of changes, maintaining a parallel bitmap
or something of liveness markers for those changes, periodically
cleaning low-occupancy segments like a log-structured filesystem by
copying their remaining live changes to a new segment, and then using
an incrementalized version of the LSM-tree-merging code that computes
partial merges of soon-to-be-superseded blocks of the LSM tree.  But
this degree of complexity seems like it kind of loses the appeal of
having the transaction caching system do everything for you
automatically, and it still doesn’t give you the option of having
queries grovel over the log of recent changes when churn is too high.

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

Relationship with dynamic scoping and graphics contexts
-------------------------------------------------------

In retained-mode graphics APIs, it’s common for graphical properties
like fill color, line width, font, and transformation matrices to be
implicitly inherited from parents to children in a
hierarchically-nested scene graph; CSS properties in HTML and SVG are
examples.  In immediate-mode graphics APIs, such as PostScript,
`<canvas>`, and even TeX, these are typically implemented as a large
number of stateful variables instead, whose values are saved and
restored using a stack of graphics states, for example using `gsave`
and `grestore` in PS, `.save()` and `.restore()` in `<canvas>`, or
`{}` in TeX.  The same set of tricks used for dynamically-scoped
variables in Lisp are applicable — shallow binding for best read
performance, deep binding for fastest context switching — and indeed
such variables were one of the major arguments for retaining “special
variables” in Common Lisp and adding `dynamic-wind` to Scheme.

This operation of temporarily obscuring the “global” value of a
dynamically-scoped variable with one or more stack layers of “local”
variables, then restoring it upon exit from a scope — this is all very
closely reminiscent of the process of buffering mutable-cell writes
and then discarding them on rollback.  But of course you don’t
normally want to erase everything you’ve drawn when you restore these
graphics parameters, and that’s what rolling back a transaction would
do.  Is there an underlying unifying abstraction that can be applied
to both cases?

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

How can we do better?

### Only logging writes for high-priority transactions ###

As discussed in the section about interrupt handling, if a piece of
code is protected from interference by other concurrent
transactions — for example, by not allowing any of them to commit — it
is guaranteed not to need a retry.  So in that case the transaction
mechanism is only providing error recovery, and for that the
transaction system need only be involved in writes of transactional
variables, not reads.  This reduces the overhead of the transaction
system by about an order of magnitude for these transactions, perhaps
to less than a factor of 2 for conventional mutable code.  (If we
write transactional variables back to their home locations while doing
this, we can use normal memory reads to read transactional variables
inside the transaction.)  Compiling code for latency-sensitive
transactions in this way may be a worthwhile optimization.

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

However, as mentioned in the filesystem example, reducing the number
of mutable variables *too far* will cause unnecessary contention and
thus reduce system throughput, either by optimistic concurrency
control retrying transactions or by pessimistic concurrency control
blocking them.  In some cases, we would actually benefit by
introducing extra mutable variables to permit higher levels of
concurrency.

### Aggregation ###

Aggregation is another common way to reduce the cost of read barriers,
write barriers, and dependency tracking for incremental computation
and rollback.  The idea is that, by agglomerating mutable variables
into larger units, we can reduce the work needed to track them,
though, as above, reducing our potential concurrency as well.  (Maybe
this is the same idea under a different name.)

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

We could imagine a filesystem or similar tree structure in which the
degree of detail we retain about a transaction’s writes varies
dynamically: perhaps after we’ve accumulated a bunch of before-images
of sibling “files” that are all being modified at once in a single
transaction, we throw up our hands and save a before-image of the
whole parent “directory”, thus avoiding any further requirement to
interpose write barriers on anything within it.

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

### Eliding unused rollback points ###

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

(Also, see above about the relationship with REST; the system can be
extended in a natural way to prevent lost-update errors in web
services.)

What about the XPra/NeWS/AJAX problem?  Above I talked about using
transactions across a distributed network under a single administrator
as a near-panacea for problems of distributed programming, a level of
optimism that surely will not pan out in real life.  XPra provides
remote access to GUI applications running on a server somewhere by
rendering their GUIs server-side and transmitting the screen updates
using a codec such as H.264, but this suffers from both
computing-power-bottleneck problems (especially when many users share
the same server) and latency problems.  NeWS tried to solve this
problem by allowing the application author to upload snippets of
PostScript code to the window server, which could then react instantly
to user interface events and do as much rendering inside the window
server as desirable, providing a smart-client/mobile-code solution
similar to modern AJAX webapps, but with PostScript as the client-side
programming language instead of JS.  AJAX is very good at improving
responsivity, at least when it doesn’t bloat a fucking text chat UI to
occupy a gigabyte of RAM, and especially at reducing server load.

Could distributed transactions simplify the task of programming such
applications?  This is a degenerate case of the network of worker
nodes all beating on a single master: one master, which is also a
worker, and a second worker for low latency.  Maybe they could allow
any given code to run transparently on either end of the high-latency
connection, or indeed optimistically on both ends of the connection,
with the results from the second-to-commit execution being discarded.
Transactions that only *read* the database can display their results
from the database with the possibility of being out-of-date and
needing to be re-executed (this is more or less how Meteor works,
although they aren’t called “transactions”), while transactions that
write to it would have to wait for the server to confirm before
reporting success.  I don’t know, I think there’s maybe some potential
here, but I don’t have it thoroughly thought out.

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

For example, the suggested application to REST would require the web
framework to be able to generate some kind of serializable identifier
for each mutable variable the HTML `<form>` depends on, and also to
retrieve those variables given those identifiers when the form is
returned.  Facilities like those would also allow the proxy code for
distributed nodes as described above to be written entirely at user
level.  As another example, the modal-reasoning question “what
variables would this randomly generated code write to if I ran it?”
needs to be able to abort the child transaction and then inspect its
write log.

Thanks
------

Thanks to sbp, Darius Bacon, Corbin Simpson, CcxWrk,
and especially Shae Erisson for
many very informative discussions that helped greatly with this note.
