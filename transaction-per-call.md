Suppose that you have an imperative programming language in which
every function call is associated with a new nested transaction, one
covering all mutable variables and other effects, and you handle
errors normally by rolling them back.  What does that give you?

Well, one thing it gives you is radical debuggability: because every
function call you enter has to save enough information for
backtracking if it needs to roll back.  The debugger can see this
information, and it can restart the function from the beginning as if
it had not started running; this enables efficient granular
time-travel debugging, but also, it's potentially useful simply to
look at the pending changes so far made by each of the functions on
the stack so far.  And implementing edit-and-continue in the debugger
becomes substantially easier under some circumstances when you can
restart the function you've just edited.  Also, being able to see
which transactional variables are being *depended on* at each level in
the call stack is also a potential boon to debugging, sort of like
`strace` at a per-function level.

Rolling back to the beginning of the function and re-executing it is
also a particularly simple way to support on-stack replacement
(whether for debuggability or for optimization).

Error handling becomes substantially easier.  Nonlocal exceptions are
especially popular in pure functional languages because cleanup while
unwinding the stack is unnecessary; C++ had so much trouble with this
that the STL wasn't exception-safe for several years, and, if I
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

With regard to error handling, it might be best in most cases for
aborted functions to return error values rather than automatically
propagating.  As long as these error values are either handled
(inspected to see what the error is, presumably as part of a
conditional) or moved to some kind of storage (for later debugging),
automatic propagation woud be suppressed, as in Wheat.  But if such an
error value is ignored (evaluated in void context, or stored in a
variable whose lifetime ends without being tested) it would propagate
up to the parent function.

This seems like a way to mostly cut the knot of error handling and
responsiveness, without requiring static bounds of worst-case
execution time for your entire user interface.

There are further expansions.  Suppose the transaction for a function
is logging all its reads and writes of mutable data; if it
additionally logs which function it is, any closed-over data, its
input parameters, then it becomes possible to use it for
memoization — any call to the same function with the same parameters
and closure data will necessarily perform the same writes and return
the same value, unless one of those reads is out of date.  So it's
valid to just perform those writes and return those results without
actually running any of the function's code.  This is very similar to
a build system like `make`, or to Umut Acar's "Self-Adjusting
Computation"; it provides a way to transparently incrementalize a
computation, so that it can be efficiently re-executed on slightly
modified input.  Also, it automatically derives a
guaranteed-linear-time Packrat parser from an ordinary
exponential-time recursive-descent parser.

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
