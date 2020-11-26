Thinking about hardware multithreading, blocking, waiting, the Padauk
chips, etc., it occurred to me that it might be reasonable to build a
CPU with a hardware work queue, or several, instead of interrupt
handlers and sleeping.

The idea is basically hardware multithreading, really: each work queue
item is a machine architectural state (PC, SP, and if applicable
accumulators, index registers, status flags, MMU tags, etc.), and the
CPU works through the highest-priority nonempty run queue,
interleaving instruction by instruction.  Normally whenever you
execute an instruction you enqueue the following instruction on the
same priority run queue, so threads of the same priority are run in
round-robin fashion, instruction by instruction, but there's a
mindelay field in the instruction encoding which forces the thread to
pause for 1-4 cycles.  So each queue item is tagged with the earliest
cycle when it is runnable.  This allows interleaved realtime tasks of
the same priority to maintain precisely the same timing regardless of
how many other tasks of that priority are running at any given time,
as long as there aren't too many of them.

(Perhaps the delayed tasks should be initially stored in a separate
queue, or perhaps each queue should practice EDF scheduling, although
that seems potentially tricky to do in hardware.)

Interrupts are handled by enqueuing a new top-half task at high
priority, either on a special interrupt queue or on the regular queue.
In a sense an interrupt handler is just a thread that is usually
asleep, but usually we don't allocate it hardware registers to store
its state between invocations.

There's an instruction to terminate a task, making its hardware state
available for forking, and a Redcode-like SPL instruction to fork.

If you have a hardware watchpoint facility, you could provide an
instruction to sleep a thread until a given memory location is written
to, that location becoming one of the hardware watchpoints.  The store
units simply need to check their destination address against the list
of watchpoints and conditionally awaken a task.  As an alternative to
external interrupts, you could simply sleep a thread until an I/O port
is "written" by the outside world.

The Tera MTA included a facility for a thread to preallocate a set of
threads it was going to spawn off, which could atomically succeed or
fail; if it succeeded, subsequent thread-spawning instructions from
that preallocation were guaranteed to succeed.  (And I think that if
you didn't use it, or exceeded the allocated capacity, they were
guaranteed to fail.)

If trying to spawn a thread with SPL when all the thread slots are
full is treated as a sort of processor exception, one of the possible
ways to handle it is to invoke an "OS" that maybe "swaps" one or more
threads to RAM, saving their architectural state — the usual OS
context-switch code, that is.  It wouldn't even have to save the full
architectural state if the SPL instruction normally clobbers some of
your registers, so it could be as efficient as a cooperative
context-switch.

Another possible response to spawning a thread when all slots (at the
requested priority level) are full is to enqueue the starting of that
thread for later — the thread once running is real-time, but starting
it is best-effort, much like in the old circuit-switched telephone
system.

A slightly alternate design: A b A c A b A d ... static timeslots
-----------------------------------------------------------------

Hmm, what happens if we have run a mindelay-4 instruction from thread
A, then a mindelay-3 instruction from thread B?  One or the other is
going to miss their deadline!

Here's an alternative: we have, say, 4 real-time "queues", A B C, each
of which has up to one real-time task, and a fourth hardware task D, a
best-effort task.  A runs every other cycle (or idles the machine), B
runs every 4th cycle, and C and D run every 8th cycle; the sequence is
A B A C A B A D, A B A C A B A D... forever — except that if any
real-time task is idle in a given cycle, because of having run a sleep
instruction, best-effort task D is instead run.  D may be a
round-robin alternation between various best-effort tasks, either in
hardware or in software, and may have its own real-time task that
preempts its best-effort task.

In this way, every instruction in queue A has mindelay a multiple of
2, every instruction in queue B has mindelay a multiple of 4, every
instruction in queue C has mindelay a multiple of 8, and they are all
guaranteed to be reawakened at the cycle-exact time they request.  A
thread-local architectural register can provide a shift to the
mindelay field, so that for example if the field is 2 bits then the
possible values of mindelay with shift 1 are 2, 4, 6, and 8; the
possible values of mindelay with shift 2 are 4, 8, 12, and 16; and the
possible values of mindelay with shift 3 are 8, 16, 24, and 32.  This
shift is in effect the priority of the thread; a thread running with
shift 3 can run in queue A, B, or C, while a thread with shift 2 can
only run in queues A or B.  So the machine in this form can run up to
three threads with shift 3 or less, two threads with shift 2 or less,
and one thread with shift 1.

In this form it would be an error to try to change your shift to 1, or
launch a shift-1 thread, if there's already a thread running in queue
A.  If the machine is running three real-time threads with shift 3, it
will only be running real-time threads ⅜ of the time, ceding the
others to the best-effort thread, but if it is running two real-time
threads with shift 2, it will be running real-time threads half the
time.  If it is running a shift-1 thread, a shift-2 thread, and a
shift-3 thread, then it will be running real-time threads ⅞ of the
time.

(Actually I guess thread D could also be running a shift-3 real-time
thread, if it's not a best-effort thread; it might make sense to make
the best-effort thread be a separate thing.)

In theory a shift-3 thread doesn't care whether it's in queue A, B, or
C, because in any case it gets one out of every 8 cycles.  There might
be phase offset questions it cares about.

With this approach, spawning an interrupt thread seems like it will
necessarily have worse interrupt latency (both worst-case and jitter)
than more traditional approaches, because where do you spawn the
interrupt handler thread?  What shift do you run it at?  If you spawn
it at shift 1 in task A, you still have at least 2 cycles of latency,
but in order to occasionally do that, you have to *never* run
*anything* other than interrupt handlers at shift 1.  If you spawn it
at shift 2, you can run one other task at either shift 1 or shift 2,
but not both; and moreover it will take 1-4 cycles before its time
slot comes around, and each additional instruction of handler adds 4
cycles more.  For purposes of scheduling, effectively the interrupt
handler is a task that always exists — you have to allocate it a task
slot.

A perhaps more interesting approach is to have, say, 8 time slots that
are rigidly round-robined among in this way, but to run a task at
shift 1 or shift 2, you must allocate respectively 4 or 2 slots to it,
thus diminishing the total number of real-time tasks by, respectively,
3 or 1.  This is sort of like buddy-system malloc: two shift-3 time
slots can Voltron into a shift-2 time slot, and two shift-2 time slots
can Voltron into a shift-1 time slot.

In the simple form, there isn't an allocation policy that permits full
usage but makes fragmentation impossible — a simple adversarial
allocation sequence guarantees maximal fragmentation:

- x0 = allocate(shift=1)
- x1 = allocate(shift=1)
- x0.free()
- x00 = allocate(shift=2)
- x01 = allocate(shift=2)
- x00.free()
- x000 = allocate(shift=3)
- x001 = allocate(shift=3)

and so on until all 8 time slots are allocated with known
buddy-pairings.  Then you can deallocate half the time slots (x001,
x011, x101, and x111) in a way that doesn't permit any of them to be
coalesced, because their buddies are still allocated; this permits no
shift-2 or shift-1 tasks to start, even though the machine is still
half idle.

Above it was suggested that *launching* threads might not be a
real-time task, even if *running* them is.  A different take on that
is that launching a thread can shift you into a different timeslot,
causing a phase shift in whatever waveforms you're embroiled in — so
in the above adversarial example, whatever shift-3 thread tries to
launch a shift-2 task can get reassigned to a different shift-3
timeslot in order to defragment a shift-2 timeslot.  That doesn't,
however, allow you to launch a shift-1 timeslot.

Another angle on that approach is suggested by Redcode: perhaps the
SPL instruction divides your previous timeslot between the two child
threads, which I think is actually what the Padauk microcontrollers do
in real life — initially the processor runs one thread at 8 MHz, but
when it splits into two, each runs at 4 MHz.  In the above
terminology, SPL raises your "shift" by 1 and spawns a child thread
with the same new "shift".  This suggests that the way to deallocate
is to run a MERGE or WAIT instruction which waits until the other
thread executes DIE or HALT or whatever and then drops your "shift" by
1.  This incarnation of SPL requires no failure handling but may be
somewhat inflexible.

The actual timeslot array in the processor might be something like a
3-bit counter and 8 3-bit pointers, each pointing to one of 8 register
sets.
