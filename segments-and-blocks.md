Consider the problem of efficiently implementing some kind of virtual
machine, like [Veskeno](veskeno-outline.md) or the JVM.  Often it’s
desirable for the virtual machine to be able to provide bounds
checking and garbage collection, thus preventing indexing, type, and
memory-management errors from provoking entirely unpredictable
behavior.

Run-time bounds checking is expensive, though, so it would be nice to
avoid it most of the time.  The current standard approach to this is
to hope your optimizing compiler will be able to hoist your bounds
checks out of your inner loops.  But I think there is a simpler and
more orthogonal approach.

Exploring this, I think I found a way to write a featureful and
adequately fast multitasking system with memory protection on
microcontrollers, perhaps similar to Liedtke’s pre-L4 designs,
L3 and Eumel; it ought to straightforwardly support paradigms like
transactional shared memory, ACID transactions, and access to
filesystem snapshots, and even helps to support clustering.

Safe indexing without bounds checks
-----------------------------------

The 8086 and its descendants index the general-purpose register file
in almost every instruction.  The general-purpose register file
consists of 8 registers (16-bit registers in the 8086, 32-bit in the
i386, 64-bit in amd64), but no bounds-checking is required, because
the index field in the instruction is only 3 bits, so indexing errors
are impossible.  (Amd64 adds additional instruction formats that can
index a larger 16-register general-purpose register file, using 4-bit
fields.)

Similarly, every memory reference on the i386 in protected mode
indexes into some 4096-byte page with the last 12 bits of the
effective address.  This indexing, too, avoids any bounds
checking — although the more significant 20 bits of the memory address are looked
up in the processor’s TLB, and if they are not found, a tree traversal
is performed, with the possibility of a protection fault if no page is
mapped.

So suppose our virtual machine provides access to pointer-free “string
memory” in, say, 1024-byte blocks, and a virtual-machine instruction
to index into the current block with an 10-bit index.  A bytecode loop
running in the virtual machine can freely generate such indices and
read and write the current block without incurring any expense of
bounds-checking.  Of course, that the array or record being indexed by
the virtual machine may be smaller than 1024 bytes, and wrapping
around to the beginning may not be an acceptable handling of
overflowing those bounds, so this may not provide bounds-checking from
the point of view of the high-level language implemented — but it
prevents the bytecode from corrupting the virtual machine’s data
structures.

Multiple block keys
-------------------

Suppose we want to access more than 1024 bytes in our program?  We can
have multiple block pointers in “segment descriptor” or “block
descriptor” or “block key” registers in the virtual machine.  4, 8, or
16 might be a reasonable number.  How do we specify which segment to
use?  There are many possibilities.  The read-string-memory and
write-string-memory instructions could contain a segment field
indicating which register to use; the virtual machine could provide an
instruction that sets the current segment to one of the segment
registers; different modes of accessing memory could use different
current-segment registers (for example, instruction fetch, data read,
and data write); you could use the 8086 “instruction prefix” mechanism
where non-default segment registers are selected for a single
instruction by a special instruction before it; or some combination.

Implicitly using a current-segment register avoids indexing into the
array of registers in the virtual-machine implementation.  Using a
separate current-write-segment register for write access potentially
permits enforcing read-only access to data.  Even if no read-only
*restrictions* are desired, in a virtual-memory system with no MMU,
this would allow the block to be efficiently marked dirty so that it
could be flushed back to stable storage; perhaps transactional memory,
copy-on-write sharing, and checkpoint and rollback could be supported
in this way as well.  Similarly, explicitly selecting blocks for
reading would efficiently give an LRU eviction system the data it
needs to work, as well as permitting them to be faulted in from slower
storage if needed.

Nodes
-----

But suppose we want to access more than 8192 bytes of data in our
program?  We need some way to store the referents of block keys, but
we cannot store them in blocks themselves — the program could
overwrite them.  Instead let us store block keys in a different kind
of structure, which following KeyKOS terminology we will call a
“node”.  A node contains, say, 64 block key slots.  The virtual
machine contains a “current node register” analogous to the “current
block register”, and a set of 4–16 node registers analogous to the
4–16 block or segment registers.  A “read block key” instruction
takes a 6-bit index and loads the corresponding block key into the
current segment register; the analogous “write block key” stores the
block key from the current segment register into the specified block
key slot of the current node.

All the block-key slots of a node and all the block-key registers of
the virtual machine are guaranteed to contain valid block keys at all
times, so these virtual-machine instructions need do no validation.

64 block keys in a node give us access to 65536 bytes of data, and we
can keep keys to another, say, 7168 bytes in the, say, 7 non-current
block-key registers.

But suppose we want access to more than 72704 bytes of data?  For that
we use multiple nodes.

Node keys
---------

In addition to the 64 block keys, a node *also* contains (say) 64
*node* key slots, and there are analogous “read node key” and “write
node key” instructions which permit traversing and mutating the graph
of nodes.  Like the block-key instructions, these instructions require
no validity checking; there is no way to copy a block key into a
node-key slot or vice versa, and there is no way to copy either kind
of key into a block or to copy data from a block into a key slot.

The read-key and write-key instructions have indirect versions that
take the indices of a slot within a node from a virtual-machine
register rather than the instruction itself.  This permits
programmatic indexing of the node graph without unwieldy 64-way
conditionals or self-modifying or dynamically-generated code.

There is nothing to prevent the node graph from being arbitrarily
cyclic or to prevent nodes from becoming unreferenced, but a garbage
collector can safely traverse this node graph.  Because the nodes are
so large, 256-1024 bytes, this should be a relatively short
process — a machine with 16 gibibytes of RAM and 64-bit pointers cannot
accommodate even 16'777'216 nodes, and if the nodes are being used to
index a tree of blocks in RAM, there can’t be even 262'144 nodes.  So
a full garbage collection should normally be submillisecond.  The
corollary, of course, is that these nodes are not going to be a
reasonable way to implement small data structures like a Lisp “cons”
or “pair”, costing at least some 64 times as much as a reasonable
cons.  Still, compared to CPython or Perl, that’s still not that much.

The repertoire
--------------

So, the full inventory of operations is something like the following:

* read-string-memory(bk, u10) → u8 or u32 or something via the given
  block key, which may be implicit for efficiency;
* write-string-memory(bk, u10, u8 or u32 or something),
  analogously — these two operations might come in multiple widths;
* allocate-block() → bk, allocates a fresh block (with the
  destination location perhaps implicit);
* allocate-node(bk) → nk, allocates a fresh node all of whose block
  keys initially refer to the block given by bk;
* read-block-key(nk, u6) → bk, reads a block key from the node given
  by nk in the slot given by u6;
* write-block-key(nk, u6, bk), analogously;
* read-node-key(nk, u6) → nk, analogously to read a node key;
* write-node-key(nk, u6, nk), analogously.

Also, possibly one or more of the following:

* select-write-block-key(u4), start using the block key in the
  identified block key register for write operations;
* select-block-key(u4), analogously but either for data read
  operations or for all operations;
* far-call(u4, offset), transfer control to the code at the given
  offset in the block whose key is in the identified block key
  register; this is not applicable if the virtual machine’s code is
  not itself stored in blocks;
* select-node-key(u4), start using the node key in the identified node
  key register;
* block-key-prefix(u4), use the block key in the identified block key
  register for the next operation only.

To index a larger memory area than a single block, you could use an
operation sequence something like the following:

* r2 := r1; supposing r1 has the index
* r2 >>= constant 10
* select-block-key(r2); supposing the current node is an index of a
  64KiB memory area
* read-string-memory(r1); supposing read-string-memory only pays
  attention to the low 10 bits.

In the case of accessing a multi-word chunk of data from a block, the
first three operations can be amortized over many accesses to the same
block.

Matrices
--------

The once and future king of computer applications is numerical
matrices, with applications such as matrix-vector multiply xGEMV,
matrix-matrix multiply xGEMM, and eigenvalue computation
xSYTRD/xGEBRD/xSTERF/xSTEDC accounting for a good deal of the usage of
many computers — historically due to physics models, now due to
artificial neural networks.

The obvious way to organize a matrix for access locality in a
blocks-and-nodes system is to divide it into rectangular or square
blocks; if it’s 32-bit single-precision, an 8×8 block fits into a
256-byte storage block, and in 64-bit double-precision, two 4×4 blocks
do.  In SGEMV matrix-vector multiply, multiplying an 8×8 matrix block
by an 8-element vector segment yields an 8-element partial-sum vector
segment in 64 multiply-accumulates; in SGEMM matrix-matrix multiply,
multiplying two 8×8 matrix blocks yields an 8×8 partial-sum matrix
block in 512 multiply-accumulates.

These seem likely to be sufficiently large amounts of computation that
the cost of faulting in a block will not be overwhelming, particularly
if any I/O latency can be hidden with multitasking.

The *other* king of computer applications is slinging around pixels to
put on the screen, and a similar 8×8 block of 32-bit BGRA pixels seems
like a good fundamental unit to use there.

Related systems
---------------

As mentioned above, Jochen Liedtke wrote some systems somewhat similar
to this design before writing L4, providing memory protection and
process isolation on Z80-based systems with what I understand to be a
trusted compiler.

### The Burroughs B5000 ###

The Burroughs B5000 is probably where this kind of structure derives
from originally, but I still need to read THE DESCRIPTOR to learn
about it.

The B5000 tagged every 48-bit memory word with a code/data bit, thus
providing “W^X” functionality at a memory-word level rather than a
page level; its descendants added two more tag bits, providing dynamic
typing at the hardware level, so that for example only a single ADD
instruction was needed, dynamically dispatching to single- or
double-precision addition; its “descriptors” indicated whether an
array contained words or bytes (and, if bytes, bytes of which of the
three supported sizes.)

### The relation to KeyKOS ###

As I mentioned above, this is in some sense copied from KeyKOS,
although there are some differences.  KeyKOS didn’t statically
segregate block keys (“page keys”) from other kinds of keys, and it
didn’t have “block key registers” or “node key registers” or any key
registers other than the ones in the nodes.

KeyKOS had various other abilities.

It used the IBM 370 virtual-memory mechanism, and later the SPARC
virtual-memory mechanism, to let the “virtual machine bytecode” be the
regular CPU instructions, mapping many-page segments with the MMU so
that the four-instruction sequence above was just a regular memory
access.

Space and time were divided up hierarchically with “space banks” and
“clocks” — you needed access to a non-exhausted space bank to
allocate space and a non-exhausted clock in order to consume CPU time.
The owner of a space bank could revoke all the storage allocated from
it.

It had kinds of keys other than page keys and node keys — it had
invocation keys and resumption keys supporting efficient remote
procedure call between separate processes (“domains”), as well as keys
granting access to other kinds of kernel objects such as space banks
and clocks.

There was a “weaken” operation that could convert a normal node key or
page key into a “sense key” which only permitted read
operations — transitively, so that if a page key was fetched from a node via a
sense key, that page key would also be returned as a read-only sense
key.

There was a closely-held KEYBITS key to obtain the raw bits of a key,
so that efficient lookups by key value were possible, though I think
all processes were able to compare two keys for equality.

KeyKOS was transparently persistent: periodically it would stream out
to disk all the dirty pages and nodes, then commit a checkpoint.

But I think even the minimal nodes-and-blocks structure described
above is enough to be useful.

### The relation to Forth ###

Forth systems traditionally used a very simple manual virtual-memory
system instead of a filesystem. `2303 BLOCK` would ensure that
1024-byte block number 2303 from the disk was loaded into a block
buffer, and return the address of that buffer; `UPDATE` would mark as
dirty the last block thus referenced and ensure that it would be
written to disk when necessary.  Block eviction was guaranteed LRU,
there were always at least two block buffers (GForth uses 20), and
multithreading was cooperative, so you could be sure that the
addresses of the two most recently referenced blocks would remain
valid until you referenced another block or yielded control.

Forth does not make any attempt to separate pointers from other data
or to check bounds on array indexing.

### The relation to Smalltalk ###

A Smalltalk method normally runs with access to some local variables,
including its arguments; a vector of instance variables in its
receiver; and a pool of constants associated with, I think, the
method.  Different bytecodes are assigned to load and store from each
of these “segments”, except that the constant pool is not writable.
There are no indirections there; the offsets are all hardcoded into
the instructions.  Arrays are instead treated as a separate class of
object whose `#at:` and `#at:put:` methods are “primitives”, handled
by native code linked into the virtual machine.

Smalltalk does not have a notion of “pointer-free data”; its SmallIntegers,
characters, booleans, and symbols (“selectors”) are treated as
full-fledged objects and nominally accessed by sending them messages,
although some of them normally are implemented by storing all their
(immutable) data in a tagged pointer rather than boxed in memory like
CPython.  Some selectors like `#ifTrue:ifFalse:` are special-cased by
the virtual machine.

(Hmm, actually maybe Smalltalk does have such a notion: “bits”
fields.)

So in a sense this is a simplification of the Smalltalk model, with
just one uniform kind of node for instance variables, local variables,
etc., but with storage for pointer-free bytes slapped onto the side.

Kaehler & Krasner’s 1982 LOOM paper describes an approach that is very
similar in many ways, although unfortunately they had not yet finished
the system at the time they published their paper, saying, “Our LOOM
virtual memory system is in its infancy.  We are only beginning to
make measurements on its performance.”  Other authors of the LOOM
system included Althoff, Weyer, Deutsch, Ingalls, and Merry, with
input from Bobrow and Tesler.

LOOM maintains an in-RAM cache of up to
2<sup>15</sup> “resident” objects linked together with 16-bit short
Oops, out of a possible total of 2<sup>31</sup> objects on disk
(occupying a maximum of 2<sup>33</sup> bytes, since it was 1982),
linked together with 32-bit long Oops.  Nonresident objects’
ambassadors in RAM are called “leaves”.  They mention that the average
object in their system consumes 13 words in memory (26 bytes), plus
perhaps a couple more words in the Resident Object Table.  To save
RAM, some short-Oop fields are just 0 (“lambda”) instead of pointing
at leaf objects, requiring LOOM to refetch the on-disk object to find
the long Oop they’re supposed to refer to.

LOOM de-lambda-izes the entire receiver, fleshing out lambdas into
full leaves, before invoking a method.  Thus it avoids null checks on
every field access.  This is reminiscent of the
microcontroller-focused mechanism described above which brings blocks
or nodes into memory when their keys are brought into a
virtual-machine register.

Their short-Oop mechanism is table-based, unlike HotSpot’s compressed-Oop
mechanism, which represents a 64-bit object pointer as a 36-bit (?)
offset from a global heap base address, shifted right by 4 (?) bits
and thus stored in a 32-bit word.  Being table-based permits relocation of objects
when their 4-word leaves are replaced by full-fledged resident objects
after being brought in from disk.  They do suggest using precisely
HotSpot’s compressed-Oop approach to support 2<sup>36</sup> bytes of
on-disk objects, though, and their RAM is 16-bit-word-oriented, so
they can support 131072 bytes of objects in RAM, like the original
Macintosh 128K, not merely 65536.

LOOM used reference counting for garbage collection, both on disk and
in RAM.

Running on microcontrollers
---------------------------

This block-and-node system solves a lot of the problems that make
bunches of microcontrollers a pain to program with even the kind of
general-purpose software we had on 1970s home computers, despite
nominally having tens or hundreds of times as much computational
power.

### Virtual memory with 256-byte blocks as pages ###

Using the loading of block key registers to drive a non-hardware-supported
virtual-memory system should permit, for example, implementing a
reasonably featureful and performant virtual-memory system on an AVR
with an SPI Flash chip, perhaps with a somewhat smaller block size,
like 256 bytes, and a somewhat smaller node size.  At 5 megabits per
second, a reasonable SPI speed, 256 bytes should take 409.6
microseconds to load or store, plus whatever overheads exist (I think
about 25% on SPI itself?  Plus erase time for Flash?)

Nodes should probably have 32 node keys and 32 block keys.  Block keys
of 32 bits in stable storage could address up to a terabyte, which is
not too limiting; 128 bytes of such block keys would be 32 block keys,
and it’s probably reasonable to use a similar number of node keys.  In
RAM, such a node might shrink to 64 bytes; it probably isn’t necessary
to keep the 32-bit identifiers of nonresident nodes and blocks,
because the extra latency to read 4 bytes from an arbitrary location
in Flash is small, unlike spinning rust.  (This of course suggests
that the whole program of using virtual memory for such a system may
be bad...)

### No barrel shifters ###

Hardware without fast bit-shifting abilities, such as an AVR, might
benefit in another way from 256-byte blocks: they could eliminate the
need for a shift operation to compute the block-slot index from a flat
address into an 8192-byte tree.

### Multiprocessing and concurrency ###

A potentially interesting approach to the problem of personal
computing on microcontrollers would be to share access to “disk”
blocks using a MESI or similar cache-coherency protocol, with these
“blocks” of 256–1024 bytes playing the role of cache lines.  Then
runnable processes can be migrated to whatever processor is idle, like
on SMP.  (You could presumably do the same thing on a Linux-like
system with a SAN, running MESI at page granularity; has anybody tried
this?  Maybe Amoeba?)

Normally, in MESI, if a cache line is in Modified or Exclusive state,
a request from another cache to read it immediately transitions it to
Shared state, guaranteeing forward progress.  But there are possible
alternatives; for example, you could “lock” a block or node for
writing, so that attempts by other processes (on the same processor or
not) to access that block or node will have to wait until you unlock
it.  Or, all blocks and nodes might be “copy-on-write” in the sense
that each process writing to them has its own private copy, and all
shared data might be immutable, with keys to new data transmitted
explicitly via some kind of IPC mechanism, or some small safety valve
for mutable data.  Or, writes might use compare-and-swap semantics:
multiple processes might be writing to the same page or node at the
same time, but when the first of them commits its write, the others
are aborted, either immediately or when they attempt to commit.
(Presumably they can then be automatically retried.)

It’s tempting to suggest that these mechanisms would make it easy to
build highly concurrent shared mutable data structures, but history
has not been kind to such optimistic statements.

### Memory buses and hardware ###

The AVR itself supports SPI with I think an 8 MHz clock, but slower
signals are less demanding on PCB layout.  Also, some common SPI
memories don’t support such high speeds; according to file
`jellybeans-2016`, the US$2.78 two-megabit STMicroelectronics
M95M02-DRMN6TP EEPROM is only 5 MHz.  Others do; the US$1.09
256-kilobit Microchip 23K256-I/SN SRAM claims 20MHz according to file
`low-power-micros`, and the US$0.36 4-mebibit Winbond W25X40CLSNIG
claims 104MHz.  Memories cheaper than that tend to be only 400kHz I²C.
I don’t know how fast SD cards’ SPI interfaces are, but they’re also
required.

If the SPI interface or whatever supports DMA, it might be feasible to
run a second process for a couple thousand cycles while the first one
was blocked on loading a block from external storage.

I’m not sure what the connectivity between multiple processors and the
“disk” should look like; I²C tends to be only 400kbps, which would
push block access times up to a spinning-rust-like millisecond level,
and SPI is inherently single-master, so you couldn’t connect multiple
microcontrollers directly to a single memory chip.  The CAN bus might
work, but of course memory chips don’t support it directly.

Probably you’d end up either connecting the processors into a ring,
each with locally attached SPI memory, or dedicating one or two
“kernel” processors to I/O arbitration, with a direct link to the
memory and another direct link to each application processor.

### Dynamically loading code blocks on a microcontroller ###

There are a few different ways a microcontroller like the AVR could
handle dynamically loading code.  First, it could just not do it at
all, just using all this segments and nodes stuff to make it
reasonably easy to run a little code with a lot of data.  Second, it
could dynamically load bytecode blocks into RAM and run them in an
interpreter — the AVR is slow enough that this would be somewhat
limiting, and it’s certainly power-hungry, but this would allow
relatively quick task switching.  Third, it could dynamically load
machine-code blocks (whether somewhat dynamically created from
bytecode or compiled ahead of time) and burn them into a “transient
program area” in its Flash so it could run them, although this will
limit its lifespan.  Fourth, if it’s a microcontroller that can run
from RAM, which the AVR can’t, it could just load blocks of machine
code into RAM and run that.

### STM32 ###

Nowadays, as described in file `stm32`, it probably doesn’t make sense
to use an AVR; you should use at least a Cortex-M processor like the
STM32; a 48MHz STM32F031x4 with 16 kibibytes of RAM costs US$1.30, and
I think some STM32s are even cheaper than that.  As bonuses, you get
much lower power consumption and the ability to run code in RAM.

### Copy-on-write ###

Copy-on-write is a little bit tricky, in that, if the same process or
transaction refers to the same block via two different access
paths — such as via block key register 3 and block slot 5 in some
node — you probably want it to get the same version of the block.  So it
isn’t sufficient to do the pure-functional-tree thing of “modifying” a
pointer to the block by creating a new version of the node, and its
parent node, and so on up to the root of the tree, because there is
perhaps no tree.  Instead, every time you go to load a block register,
you must do a table lookup to see if the current process/transaction
has a modified copy of that block, and, if not, conditionally create
one.  (And analogously for modifying nodes.)

### The J1A ###

A potentially more interesting kind of microcontroller to use for this
is the J1A Forth-like processor.  It might be reasonable to extend it
to do many of the block and node operations “in hardware”, run several
processors concurrently inside a single FPGA, and perhaps reconfigure
other parts of the FPGA dynamically to assist with other computations.

Incremental and differentiable computation
------------------------------------------

Above I mentioned transactional memory for concurrency control as one
possible application of this kind of virtual machine.  The idea is
that, to access the memory, you run some code inside a *transaction*,
giving it some inputs when you start it, and buffer all its memory
writes in a copy-on-write fashion; if the transaction runs to
completion successfully, it tries to *commit*, at which point we check
to see whether any block or node it read had been modified by some
other transaction in the mean time.  If so, we *abort* the
transaction, discarding all of the buffered written data, and
transparently restart it from the beginning; if not, it successfully
commits, and its versions of that modified data become the active
versions.  It’s a very simple idea, and it is commonly used to permit
high levels of parallelism with very straightforward, non-bug-prone,
semantics.

As one example, you might have a piece of code that scans for an
occurrence of the word “fuck” in a file, and sends an alert email if
it appears, and another piece of code that modifies the contents of
the file.  If the scanning code happens to be reading through the file
when the word “full” is overwritten with the word “sick”, it might
incorrectly conclude that the word “fuck” occurred, and send a
spurious email, possibly getting someone fired.  But if both pieces of
code must run within transactions, which must commit for any
externally-observable thing to happen, then any modification to the
blocks read by the scanner will abort the scanner’s
transaction — unless it doesn’t commit until after the scanner
commits, in which case the scanner will see a consistent
post-modification version of the file.

Thus this simple optimistic-synchronization rule makes the
transactions perfectly serializable — the results are exactly the same
as if all the transaction code had run in a single thread, in the
order in which the transactions committed — and it guarantees forward
progress.  There are various kinds of optimizations that can be made
to improve such a system’s performance.

### Long transactions ###

Consider, though, the situation of this scanner running on a large
disk partition on which files are frequently being created and
destroyed.  Although the system never blocks, the scanner will never
finish!  By the time it comes to the end of the disk, certainly some
other program will have modified some blocks it had already scanned,
thus invalidating its results, and so it will be automatically
restarted.

There are many ways to handle this “long transaction” problem; among
them, pessimistic synchronization, nested transaction memoization,
relaxed consistency, clever reordering, and spheres of influence.

#### Pessimistic synchronization ####

Pessimistic synchronization was historically the most common way to
solve the problem.  Instead of allowing all transactions to proceed,
the scanner acquires “read locks” on every block or node it reads; if
any other transaction attempts to write to such a block or node, it is
paused until the scanner’s transaction completes and then acquires a
write lock; and if the scanner tries to acquire a read lock on a block
that some other transaction already has a write lock on, the scanner
blocks until the other transaction commits or aborts.  The great
benefits of pessimistic synchronization are that no work is ever
wasted (so worst-case execution times can be computed) and no block
ever need be copied.  Its drawbacks include that it’s easy to
deadlock; it’s difficult to get good scalability, since things block
all the time; and, in real-time systems, it suffers from “priority
inversion” where a low-priority task can hold a lock blocking a
high-priority task, and a medium-priority task can then starve the
low-priority and the high-priority task.

#### Nested transaction memoization ####

Nested transaction memoization is probably not something I just made
up, but it works as follows.  The scanner scans as follows, in a
made-up programming language with block arguments:

    scan(word, file, start, end) = {
        return child_transaction {
            assert(word.len < blocksize)
            if (end - start < blocksize) {
                return contains(word, file, start, end)
            }

            mid = start + (end - start) // 2
            return (scan(word, file, start, mid + len(word) - 1) or
                    scan(word, file, mid, end))
        }
    }

`scan` starts by spawning a nested child transaction which can commit
or abort before its parent does — by default, its abort will just
retry it without affecting its parent, but once it commits, the blocks
and nodes it read and wrote are added to the read and write sets of
its parent, so any *later* changes to the blocks it read will then
abort the parent; but there are some significant fillips we will see
below.

If the area to scan is smaller than `blocksize`, then the scan is done
directly, using a naïve string search or Boyer-Moore or whatever.  We
presume that this can be done quickly enough that, much of the time,
we will finish before something else overwrites any of the data in
that range, so our chance of being aborted is small.

Otherwise, `scan` proceeds by making two recursive calls to itself,
which of course spawn their own nested transactions.  If, during a
commit, some read block is found to have been overwritten by a
concurrent transaction, that transaction is then retried; but its
earlier siblings remain committed.

So far, this seems to have ameliorated our problem only slightly: if
something writes to the third quarter of the file while the fourth
quarter is being scanned, then the transaction scanning the second
half of the file will be aborted and retried.  So our tiny chances of
success, assuming a uniform distribution of write traffic, would seem
to have improved only by a factor of 4, or less.

This is where *memoization* comes in and saves the day!  Suppose that,
instead of only remembering a flat list of blocks and nodes read and
written by each *active* transaction in the stack, we also remember
those read and written by *committed* transactions that are children
or descendants of some active transaction, as well as the code and
environment state needed to re-execute those transactions.  Now, when
we retry scanning the second half of the file, we can *revalidate*
these read sets, and if they are still valid, we can “wink in” the
write set without actually running any of the transaction code.

To be concrete, suppose the file consists of eight blocks (0, 1, 2, 3,
4, 5, 6, and 7), and we are retrying scanning the last four blocks
because block 5 has changed.  (I will disregard overlaps here.)  The
transaction to scan blocks 4, 5, 6, and 7 is invalid, so it begins
re-executing, and the first thing it does is to spawn a child
transaction to scan blocks 4 and 5.  This child transaction is
invalid, since block 5 has changed, so it spawns a child transaction
to scan block 4.  So far, memoization has changed nothing.

But then a miracle occurs!  Block 4 hasn’t changed, so it doesn’t need
to be scanned; the `False` return value and (empty) write set of the
block-4 transaction are instantly retrieved from the memo table.  We
proceed to spawn a child transaction to scan block 5, which has
changed, so we rescan it byte by byte.  It also returns `False`, and
so the blocks-4-and-5 transaction returns `False`, and its parent
transaction spawns a new transaction to scan blocks 6 and 7.  But that
transaction is also found in the memo table!  So no code need execute;
its (empty) write set is committed to its parent, and its `False`
return value is returned.

So now our scan is complete, having scanned only the single block that
actually changed and done additional O(log N) transaction revalidation
work, through the beautiful gift of memoized nested transactions!

Like I said, I probably didn’t just make this up.  I just can’t
remember where I’ve seen it.  Maybe Umut Acar’s “self-adjusting
computation”.

Transactions that return immutable data — inevitably, newly
created — poses no problem for this approach, and neither does
mutating existing data.  But allocating and returning new blocks and
nodes does pose a difficulty for memoization, because memoization
introduces aliasing!  Without memoization, running the same
transaction twice with the same inputs (including the state of the
store) will allocate and return two separate sets of objects, but a
naïvely implemented memo system would return two aliases to the same
mutable objects.  I think this can be solved by marking the blocks and
nodes as copy-on-write, by having the memo system actually copy them
before returning them, or by making them read-only.

#### Relaxed consistency ####

A common solution to the long-transaction problem is to use more
relaxed isolation levels, at the risk of incorrect results.  No more
details will be given of this shameful practice.

#### Clever reordering, or MVCC ####

A different approach to the problem is to hope that the scanner’s
results can be retroactively inserted into the transaction history
instead of being appended to it.  This works surprisingly often; in
the example code above, for instance, the scanner doesn’t write any
blocks — its only effect is to return a Boolean value — so it can
trivially be run on any previous snapshot, and it is guaranteed that
none of the transactions that committed in the interim would have had
different results had the scanner transaction committed long ago.

This approach requires examining the write-set of the long transaction
when it goes to commit to ensure that it’s not overwriting any blocks
or nodes that any transaction committed after its snapshot had read.
If so, such a cyclic dependency violates serializability and thus
cannot be tolerated; the long transaction must be retried anyway.

This poses the question of exactly where in history to (conceptually)
insert the long transaction.  But unless we are making up a
transaction log, there’s no need to actually *compute* the
serializable order to respect transaction isolation; it’s sufficient
that one exists.  So it’s sufficient to ensure that committing the
transaction would not create a cycle in the bipartite graph of
transactions and block/node versions.

#### Spheres of Influence ####

Retrying the long transaction, however, isn’t the only possible
solution!  You could, instead, commit the long transaction and roll
back and retry the already-committed *later* transactions, as long as
no effects from them have escaped your rollback grasp.  This is the
idea of the “spheres of influence” idea from the ancient transaction
processing literature, which I found in Gray & Reuter, and it’s fairly
similar to how the US banking system works: all numbers are
provisional, subject to revision, until a few months have passed.

### Incremental recomputation ###

Above, the use of memoized nested transactions was suggested to permit
long transactions to complete successfully despite concurrent writes.
But it should be apparent that this is a form of incremental
computation: by memoizing results from previous partial computations,
incremental changes can be accommodated efficiently, even when they’re
happening too fast for a batch-mode computation to run to completion
successfully.

If the memo table is retained rather than being discarded as soon as
the root transaction commits, it can be used to incrementalize future
computations of similar transactions as well.  In a database system,
for example, this approach could largely transparently provide the
performance functionality of standard indices, materialized views, and
precomputed OLAP rollups, though perhaps not query optimization, since
its very transparency complicates its use by a query optimizer.

What policy should be used to manage memo-table entries?  Retaining
too little will waste CPU cycles and perhaps miss real-time deadlines;
retaining too much will waste RAM and perhaps also slow the system
down.  A unified memo-table-management system might be able to use
robust heuristics to come to a reasonable global optimization
solution, taking into account the observed computational cost of each
transaction; but, lacking that, you probably need some way to manually
specify the policy.

This memo table will suffer “false misses” under some circumstances
that a smarter incremental computation mechanism might be able to take
advantage of: computations that would be equivalent but end up reading
the same data from different locations, for example, and in ABA cases
where a location changes twice, ending with the same value it started
with (a counter being incremented and then decremented, for instance).

### Parallel computation with nested transactions ###

In the example code above, the child transaction results were used
immediately; the parent transaction blocked until the child
transaction was finished executing.  But in many cases, including the
above, it would be semantically acceptable to spawn multiple
potentially concurrent child transactions, returning only a future for
the transaction’s output from the initial spawn call, which is *later*
blocked on — perhaps after spawning additional child transactions.

### Differentiable computation with transactions ###

To compute a Jacobian of a computation with a small number of outputs
and many inputs — the gradient, in the case that the number of outputs
is one — reverse-mode automatic differentiation is much more
efficient.  But reverse-mode automatic differentiation requires
propagating the gradient backward through the dataflow.  For a short
or highly regular computation, it’s reasonable to materialize the
whole dataflow graph in RAM at once, but not for long, iterative, and
irregular computations, since the dataflow graph can contain trillions
of nodes — in the limit, a node for every machine instruction executed
on thousands of machines over a period of hours to months.

So the usual way to do this — if I understand correctly, which I may
not — is to run the computation forward from the beginning to the end,
saving its entire state on a “tape” of periodic checkpoints.  If you
have enough space, you can take the checkpoints close enough together
that the full dataflow graph between any two adjacent checkpoints fits
in RAM; then you can iterate backward through the checkpoints,
building that dataflow graph in memory so as to propagate the Jacobian
backward to the previous checkpoint.  For the gradient case, this is
theoretically about as fast as the original computation.

If that’s too much space — perhaps a terabyte for 10 minutes of
computation — you can thin out the tape to a logarithmically-small
number of checkpoints, in exchange for a logarithmically-small (or
log-squared?) slowdown.  Perhaps instead of 1024 checkpoints, one per
second, you might have 11 checkpoints: one from 1 second ago, one from
2 seconds ago, one from 4 seconds ago, and so on up to 1024 seconds
ago.  When the time comes to propagate the Jacobian from the
checkpoint from 4 seconds from the end to the checkpoint from 8
seconds to the end, you first replay from the 8-seconds-from-the-end
checkpoint to recreate the 6-seconds-from-the-end and
5-seconds-from-the-end checkpoints.

It should be apparent that, with manual control over the memo table,
the memoized-nested-transaction mechanism described earlier can
provide an efficient, space-sharing way to periodically checkpoint a
computation — once we roll back everything that happened later, the
*end* of each memoized transaction is a point to which we can quickly
“fast-forward” from its beginning.  Actually constructing the
in-memory dataflow graphs and back-propagating the Jacobians, however,
cannot be done by the mechanisms described earlier; they require more
profound interfacing. XXX

### Streams and reactive UI updates ###

Some of the transactions described above write their output to the
block and node store.  Others, though, are merely queries that return
a value without mutating anything, at least not anything externally
visible.  By recording which blocks and nodes are read by a query
transaction, as the transaction system does, we can automatically
determine when query results have become out of date; the memoization
mechanism described above provides a reasonably efficient way to
support polling, for example for screen updates, but if writing to a
block or node can trigger an asynchronous invalidation notification
(which can be responded to by repeating the query if desired), that
may have lower latency, have higher throughput, or use less energy
under some circumstances.

Nothing in the system design limits these approaches to read-only
queries; they can apply equally well to “queries” that mutate the
block and node store in a persistent way (as opposed to using nodes
and blocks they allocate ephemerally as temporary storage, or allocate
and then return).  Indeed, if those queries write to no nodes and
blocks that they also read and did not allocate, they can be
mechanically guaranteed to be idempotent.  (But see above about
memoization introducing aliasing.)

Progress bars on such transactions probably cannot be provided through
transactional mechanisms, since they have dataflow from uncommitted
transactions.  So, as with differentiable programming,
metatransactional mechanisms are needed.

### Modular blocking and composable memory transactions ###

The [_Composable Memory Transactions_ paper][CMT], which I need to
reread, explains how to use an optimistic transactional memory with
nested transactions, like the above, to support blocking patterns of
communication by adding two more functions, `retry` and `orElse`.

[CMT]: https://www.microsoft.com/en-us/research/wp-content/uploads/2005/01/2005-ppopp-composable.pdf "Harris, Marlow, Peyton Jones, and Herlihy, 2005"

`retry` conceptually simply aborts the current transaction, causing it
to be automatically retried.  But if the system responds by beginning
to run the same transaction code again with the same inputs and the
same state of the store, it would simply deterministically reach
`retry` a second time, and so on, busy-waiting.  So a more reasonable
system, like the one they actually implemented, waits to retry the
transaction until the store has changed — specifically, until some
transactional variable that it had read before invoking `retry` has
changed.

The `orElse` operator provides a way to recover from such failures, by
composing two alternative child transactions into a larger child
transaction.  If the child transaction that is its left argument fails
because of invoking `retry` (though not because of a conflicting write
by a concurrent transaction), then control flows to the alternative
transaction that is its right argument.  If that transaction also
fails, then the transaction resulting from `orElse` fails.

Thus `retry` provides a way to convert a polling interface, such as
reading a transaction variable to see if something is ready, into a
blocking interface, while `orElse` provides a way to either combine
two sources of blocking into an alternative source that only blocks
while both sources are blocking, like Unix `select(2)`, or to convert
a blocking interface into a polling interface (by providing a second
alternative that does not block).

Precisely the same interface would work on top of nodes and blocks.

The requirements of transactional systems limit the applicability of
familiar interprocess communication.

For example, you could try to implement a byte pipe between two
transactions by a memory block whose first two words contain beginning
and end pointers into a ring buffer that is the rest of the block.  A
transaction could attempt to add bytes to the ring buffer and return
the number successfully written, blocking with `retry` if there is not
room, or to remove bytes from it and return them, blocking with
`retry` if there are no bytes to remove.  If a pipe-reader and a
pipe-writer try to mutate the block at the same time, one will succeed
and the other will fail at first, then retry and succeed.  So, at
first, this sounds like a standard Unix pipe.

But, if the pipe-reader’s parent transaction is aborted, the
pipe-reader’s modifications to the pipe block will be rolled back.  As
long as the two share a parent transaction, then all the pipe-writer’s
modifications will, too; and, until they do share a parent transaction
(that is, until any levels of transactions separating them from their
lowest common ancestor transaction have committed), their
communications won’t be visible to one another — the writer can’t
unblock the reader or provide it bytes, and the reader can't unblock
the writer.