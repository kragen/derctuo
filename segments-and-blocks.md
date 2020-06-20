Consider the problem of efficiently implementing some kind of virtual
machine, like [Veskeno](veskeno-outline.md) or the JVM.  Often it's
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
microcontrollers, perhaps similar to Liedtke's pre-L4 designs,
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
effective address.  This indexing, too, avoids any bounds checking ---
although the more significant 20 bits of the memory address are looked
up in the processor's TLB, and if they are not found, a tree traversal
is performed, with the possibility of a protection fault if no page is
mapped.

So suppose our virtual machine provides access to pointer-free "string
memory" in, say, 1024-byte blocks, and a virtual-machine instruction
to index into the current block with an 10-bit index.  A bytecode loop
running in the virtual machine can freely generate such indices and
read and write the current block without incurring any expense of
bounds-checking.  Of course, that the array or record being indexed by
the virtual machine may be smaller than 1024 bytes, and wrapping
around to the beginning may not be an acceptable handling of
overflowing those bounds, so this may not provide bounds-checking from
the point of view of the high-level language implemented --- but it
prevents the bytecode from corrupting the virtual machine's data
structures.

Multiple block keys
-------------------

Suppose we want to access more than 1024 bytes in our program?  We can
have multiple block pointers in "segment descriptor" or "block
descriptor" or "block key" registers in the virtual machine.  4, 8, or
16 might be a reasonable number.  How do we specify which segment to
use?  There are many possibilities.  The read-string-memory and
write-string-memory instructions could contain a segment field
indicating which register to use; the virtual machine could provide an
instruction that sets the current segment to one of the segment
registers; different modes of accessing memory could use different
current-segment registers (for example, instruction fetch, data read,
and data write); you could use the 8086 "instruction prefix" mechanism
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
we cannot store them in blocks themselves --- the program could
overwrite them.  Instead let us store block keys in a different kind
of structure, which following KeyKOS terminology we will call a
"node".  A node contains, say, 64 block key slots.  The virtual
machine contains a "current node register" analogous to the "current
block register", and a set of 4--16 node registers analogous to the
4--16 block or segment registers.  A "read block key" instruction
takes a 6-bit index and loads the corresponding block key into the
current segment register; the analogous "write block key" stores the
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
*node* key slots, and there are analogous "read node key" and "write
node key" instructions which permit traversing and mutating the graph
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
so large, 256-1024 bytes, this should be a relatively short process
--- a machine with 16 gibibytes of RAM and 64-bit pointers cannot
accommodate even 16'777'216 nodes, and if the nodes are being used to
index a tree of blocks in RAM, there can't be even 262'144 nodes.  So
a full garbage collection should normally be submillisecond.  The
corollary, of course, is that these nodes are not going to be a
reasonable way to implement small data structures like a Lisp "cons"
or "pair", costing at least some 64 times as much as a reasonable
cons.  Still, compared to CPython or Perl, that's still not that much.

The repertoire
--------------

So, the full inventory of operations is something like the following:

* read-string-memory(bk, u10) -> u8 or u32 or something via the given
  block key, which may be implicit for efficiency;
* write-string-memory(bk, u10, u8 or u32 or something), analogously
  --- these two operations might come in multiple widths;
* allocate-block() -> bk, allocates a fresh block (with the
  destination location perhaps implicit);
* allocate-node(bk) -> nk, allocates a fresh node all of whose block
  keys initially refer to the block given by bk;
* read-block-key(nk, u6) -> bk, reads a block key from the node given
  by nk in the slot given by u6;
* write-block-key(nk, u6, bk), analogously;
* read-node-key(nk, u6) -> nk, analogously to read a node key;
* write-node-key(nk, u6, nk), analogously.

Also, possibly one or more of the following:

* select-write-block-key(u4), start using the block key in the
  identified block key register for write operations;
* select-block-key(u4), analogously but either for data read
  operations or for all operations;
* far-call(u4, offset), transfer control to the code at the given
  offset in the block whose key is in the identified block key
  register; this is not applicable if the virtual machine's code is
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

Related systems
---------------

The Burroughs B5000 is probably where this kind of structure derives
from originally, but I still need to read THE DESCRIPTOR to learn
about it.

As mentioned above, Jochen Liedtke wrote some systems somewhat similar
to this design before writing L4, providing memory protection and
process isolation on Z80-based systems with what I understand to be a
trusted compiler.

### The relation to KeyKOS ###

As I mentioned above, this is in some sense copied from KeyKOS,
although there are some differences.  KeyKOS didn't statically
segregate block keys ("page keys") from other kinds of keys, and it
didn't have "block key registers" or "node key registers" or any key
registers other than the ones in the nodes.

KeyKOS had various other abilities.

It used the IBM 370 virtual-memory mechanism, and later the SPARC
virtual-memory mechanism, to let the "virtual machine bytecode" be the
regular CPU instructions, mapping many-page segments with the MMU so
that the four-instruction sequence above was just a regular memory
access.

Space and time were divided up hierarchically with "space banks" and
"clocks" --- you needed access to a non-exhausted space bank to
allocate space and a non-exhausted clock in order to consume CPU time.
The owner of a space bank could revoke all the storage allocated from
it.

It had kinds of keys other than page keys and node keys --- it had
invocation keys and resumption keys supporting efficient remote
procedure call between separate processes ("domains"), as well as keys
granting access to other kinds of kernel objects such as space banks
and clocks.

There was a "weaken" operation that could convert a normal node key or
page key into a "sense key" which only permitted read operations ---
transitively, so that if a page key was fetched from a node via a
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
of these "segments", except that the constant pool is not writable.
There are no indirections there; the offsets are all hardcoded into
the instructions.  Arrays are instead treated as a separate class of
object whose `#at:` and `#at:put:` methods are "primitives", handled
by native code linked into the virtual machine.

Smalltalk does not have a notion of "pointer-free data"; its SmallIntegers,
characters, booleans, and symbols ("selectors") are treated as
full-fledged objects and nominally accessed by sending them messages,
although some of them normally are implemented by storing all their
(immutable) data in a tagged pointer rather than boxed in memory like
CPython.  Some selectors like `#ifTrue:ifFalse:` are special-cased by
the virtual machine.

(Hmm, actually maybe Smalltalk does have such a notion: "bits"
fields.)

So in a sense this is a simplification of the Smalltalk model, with
just one uniform kind of node for instance variables, local variables,
etc., but with storage for pointer-free bytes slapped onto the side.

Kaehler & Krasner's 1982 LOOM paper describes an approach that is very
similar in many ways, maintaining an in-RAM cache of up to
2<sup>15</sup> "resident" objects linked together with 16-bit short
Oops, out of a possible total of 2<sup>31</sup> objects on disk
(occupying a maximum of 2<sup>33</sup> bytes, since it was 1982),
linked together with 32-bit long Oops.  Nonresident objects'
ambassadors in RAM are called "leaves".  They mention that the average
object in their system consumes 13 words in memory (26 bytes), plus
perhaps a couple more words in the Resident Object Table.  To save
RAM, some short-Oop fields are just 0 ("lambda") instead of pointing
at leaf objects, requiring LOOM to refetch the on-disk object to find
the long Oop they're supposed to refer to.

Their short-Oop mechanism is table-based, unlike HotSpot's short-Oop
mechanism, which represents a 64-bit object pointer as a 36-bit (?)
offset from a global heap base address, shifted right by 4 (?) bits
and thus stored in a 32-bit word.  This permits relocation of objects
when their 4-word leaves are replaced by full-fledged resident objects
after being brought in from disk.

It's unclear to me how LOOM's on-disk garbage collection was supposed
to work.

Running on microcontrollers
---------------------------

This block-and-node system solves a lot of the problems that make
bunches of microcontrollers a pain to program with the kind of
general-purpose software we had on 1970s home computers.

Using the loading of block key registers to drive an unprotected
virtual-memory system should permit, for example, implementing a
reasonably featureful and performant virtual-memory system on an AVR
with an SPI Flash chip, perhaps with a somewhat smaller block size,
like 256 bytes, and a somewhat smaller node size.  At 5 megabits per
second, a reasonable SPI speed, 256 bytes should take 409.6
microseconds to load or store, plus whatever overheads exist (I think
about 25% on SPI itself?  Plus erase time for Flash?)

Hardware without fast bit-shifting abilities, such as an AVR, might
benefit in another way from 256-byte blocks: they could eliminate the
need for a shift operation to compute the block-slot index from a flat
address into an 8192-byte tree.

The AVR itself supports SPI with I think an 8 MHz clock, but slower
signals are less demanding on PCB layout.  Also, some common SPI
memories don't support such high speeds; according to file
`jellybeans-2016`, the US$2.78 two-megabit STMicroelectronics
M95M02-DRMN6TP EEPROM is only 5 MHz.  Others do; the US$1.09
256-kilobit Microchip 23K256-I/SN SRAM claims 20MHz according to file
`low-power-micros`, and the US$0.36 4-mebibit Winbond W25X40CLSNIG
claims 104MHz.  Memories cheaper than that tend to be only 400kHz I²C.
I don't know how fast SD cards' SPI interfaces are, but they're also
required.

If the SPI interface or whatever supports DMA, it might be feasible to
run a second process for a couple thousand cycles while the first one
was blocked on loading a block from external storage.

There are a few different ways a microcontroller like the AVR could
handle dynamically loading code.  First, it could just not do it at
all, just using all this segments and nodes stuff to make it
reasonably easy to run a little code with a lot of data.  Second, it
could dynamically load bytecode blocks into RAM and run them in an
interpreter --- the AVR is slow enough that this would be somewhat
limiting, and it's certainly power-hungry, but this would allow
relatively quick task switching.  Third, it could dynamically load
machine-code blocks (whether somewhat dynamically created from
bytecode or compiled ahead of time) and burn them into a "transient
program area" in its Flash so it could run them, although this will
limit its lifespan.  Fourth, if it's a microcontroller that can run
from RAM, which the AVR can't, it could just load blocks of machine
code into RAM and run that.

Nowadays, as described in file `stm32`, it probably doesn't make sense
to use an AVR; you should use at least a Cortex-M processor like the
STM32; a 48MHz STM32F031x4 with 16 kibibytes of RAM costs US$1.30, and
I think some STM32s are even cheaper than that.  As bonuses, you get
much lower power consumption and the ability to run code in RAM.

A potentially interesting approach to the problem of personal
computing on microcontrollers would be to share access to "disk"
blocks using a MESI or similar cache-coherency protocol, with these
"blocks" of 256--1024 bytes playing the role of cache lines.  Then
runnable processes can be migrated to whatever processor is idle, like
on SMP.  (You could presumably do the same thing on a Linux-like
system with a SAN, running MESI at page granularity; has anybody tried
this?  Maybe Amoeba?)

Normally, in MESI, if a cache line is in Modified or Exclusive state,
a request from another cache to read it immediately transitions it to
Shared state, guaranteeing forward progress.  But there are possible
alternatives; for example, you could "lock" a block or node for
writing, so that attempts by other processes (on the same processor or
not) to access that block or node will have to wait until you unlock
it.  Or, all blocks and nodes might be "copy-on-write" in the sense
that each process writing to them has its own private copy, and all
shared data might be immutable, with keys to new data transmitted
explicitly via some kind of IPC mechanism, or some small safety valve
for mutable data.  Or, writes might use compare-and-swap semantics:
multiple processes might be writing to the same page or node at the
same time, but when the first of them commits its write, the others
are aborted, either immediately or when they attempt to commit.
(Presumably they can then be automatically retried.)

It's tempting to suggest that these mechanisms would make it easy to
build highly concurrent shared mutable data structures, but history
has not been kind to such optimistic statements.

I'm not sure what the connectivity between multiple processors and the
"disk" should look like; I²C tends to be only 400kbps, which would
push block access times up to a spinning-rust-like millisecond level,
and SPI is inherently single-master, so you couldn't connect multiple
microcontrollers directly to a single memory chip.  The CAN bus might
work, but of course memory chips don't support it directly.

Probably you'd end up either connecting the processors into a ring,
each with locally attached SPI memory, or dedicating one or two
"kernel" processors to I/O arbitration, with a direct link to the
memory and another direct link to each application processor.

A potentially more interesting kind of microcontroller to use for this
is the J1A Forth-like processor.  It might be reasonable to extend it
to do many of the block and node operations "in hardware", run several
processors concurrently inside a single FPGA, and perhaps reconfigure
other parts of the FPGA dynamically to assist with other computations.
