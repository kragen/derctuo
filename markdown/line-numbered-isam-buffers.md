Darius and I have talked occasionally over the years about the problem
of text editor buffers.  Editor buffers, like the ones in Emacs, need
to support a few operations efficiently:

1. Traversing the text sequentially, for example to repaint the screen
   or search for a string or regular expression.
2. Adding markers to the text.
3. Determining what markers are present at a given location.
4. Jumping to a marker.
5. Inserting and deleting text anywhere in the buffer.

From the point of view of the beginning of the buffer, text moves when
you insert and delete things before it.  The tricky part is that the
markers need to move with the text; it isn't good enough to just store
a byte offset for each marker.

Ideally we'd like all of these operations to be sublinear in the size
of the buffer, and we'd like the buffer to be able to be at least
nearly as big as RAM, if not the disk, and we might have many markers
per line, for example to store syntax-highlighting properties of the
text, so the number of markers also grows linearly as the text grows.
If any of these operations take linear time instead of, say,
logarithmic or at least square-root time, then they will become
unbearably slow when we open a gigabyte-sized file, much less a
terabyte-sized one.

I think Raph Levien has come up with a design for this in Xi based on
ropes, but I don't know what it is.

I was lying in bed thinking about G-code and BASIC interpreters and
the HP 3000, and I realized that you can more or less solve this with
an ISAM approach, and this is probably what Darius and I had come up
with before I forgot it until tonight.  You represent the buffer as an
(in-RAM) ISAM file with synthetic, meaningless keys.  ISAM supports
the following operations efficiently:

1. Go to the first record whose key is equal to or following a given
   key.
2. Go to the next record by key (or report failure).
3. Go to the previous record by key (or report failure).
4. Read the key and value of the current record.
5. Delete the current record.
6. Insert a new key-value pair into the file.

All of these take at most logarithmic time; 2 and 3 are typically
constant time.  (It's common for ISAM systems to support an update
operation as well, but in the absence of concurrency, this can be
synthesized from read, delete, and insert.)  There are a variety of
ways to implement this, though B*-trees and LSM-trees are the most
popular.

How does this give us buffers?  Well, when we read a file into a
buffer, we break it into blocks of, say, 256 bytes, and assign each
one a sequential string ID to serve as its key; perhaps AAA, AAB, AAC,
and so on, or if the file is a terabyte, AAAAAAA, AAAAAAB, AAAAAAC,
and so on.  When you add a marker in a block, you update the block to
include a pointer to the marker, and you store the block key and the
byte offset in the marker.

When you change text within a block, you must keep the block from
growing too large; you may need to split the block, perhaps splitting
block AAB into AAB and AABA.  This requires updating the key stored in
each marker that has moved to the new block.  If you don't split the
block, you must update the byte offset stored in each marker that
would have moved to the new block.

To ensure that traversal remains fast, you might also have to keep
blocks from becoming pathologically small, perhaps merging what little
remains of block AAD into the end of block AAC and removing AAD if
both of them have shrunk a lot.

Because traversing the blocks sequentially is fast, traversing the
buffer sequentially is fast.  Adding a marker is very fast, requiring
only an update interaction.  Finding what markers are present at a
given location is fast because it only involves inspecting the current
block, which is never very large.  Jumping to a marker is fast because
the marker contains the key to the block, which permits navigating to
it via ISAM.  Inserting and deleting may involve ISAM operations.

But why ISAM?  Undo and incremental monoids
-------------------------------------------

Why ISAM rather than just a doubly-linked-list piece table?  You could
include memory pointers to the pieces in the marker objects instead of
ISAM keys.  Inserting and deleting into a doubly-linked list is easy;
you have to update all the markers concerned, but that is true with
ISAM as well.  And ISAM adds a logarithmic slowdown to the
jump-to-a-marker operation, which would instead be constant-time with
pointers to pieces.  So is there any advantage of ISAM here?

Well, ISAM can provide FP-persistence.  Ropes are "persistent" in the FP sense: a
reference to a rope refers to a given state of that rope, so an undo
history can be implemented simply as a list of pointers to ropes that
share structure.  You can implement ISAM in an FP-persistent way, and
if the references from the buffer blocks to the markers are indirected
through an FP-persistent dictionary data structure (whether some
variant of ISAM or just a hash table) then the whole buffer structure
can be FP-persistent.

Ropes don't have an obvious way to handle markers, though.  Rope nodes
are immutable.  If you store markers in an immutable rope node, you
can copy them to a new node if you make modified versions of it,
easily supporting operation #3 --- but how do you support
operation #4, jumping to a marker?  Storing a pointer to a rope node in a marker
doesn't help --- even if that rope node *is* in the version of the
buffer of interest, you can't traverse the graph to its parent,
because it may have many parents, some of which are in the version of
interest and some of which are not.

The ISAM approach provides FP-persistence, like ropes, without losing
the ability to track down a marker; its compensating drawback is that
copying text from one buffer to another, or from one place to another
in the same buffer, requires copying all the text's characters.
(*Cut* and paste can avoid this.)

### Monoidal computations ###

(See also [Monoid prefix sum](monoid-prefix-sum.md).)

Aside from simple undo, there's another set of operations commonly
required in text editors which can be supported efficiently by ISAM or
ropes, but not in any way I can see with a simple linked-list piece
table: things like syntax highlighting, line numbers, and display
column, which are generically a monoidal computation on the sequence
of characters from the beginning of the file to a given point.

Basically the problem is that whether, say, a given line in a buffer
is line 123 or line 124 depends on all the bytes before that line;
inserting a single newline early in the buffer increments the line
numbers of everything after it, but if this takes time proportional to
the number of lines in the buffer, then it will be unusable on
sufficiently large buffers.  On the other hand, if you don't store any
line-number information, then going to a given line number will be
unusably slow on sufficiently large buffers.  (It's okay for that to
require a full buffer scan the first time, since there's no way to
avoid that, but not every time.)

#### Parallel prefix sums ####

The parallel prefix-sum algorithm offers a solution to this problem
for general monoids.  If your buffer is made up of some kind of tree
with text in its leaves, and traversing the tree left to right gives
you the order of the text in the buffer, you can cache the monoid
value for just the text within the subtree rooted at each node.  Then,
to calculate the monoid value for some prefix of the buffer, you use
the monoid operation to combine the values in the tree nodes within
that prefix, which is linear in the tree depth and thus logarithmic in
the buffer size.  Updating a leaf similarly merely requires
invalidating and potentially recalculating the cached monoid values in
its logarithmic number of ancestors.  In the case of monotonic values
like line numbers, you can also efficiently do a search for a given
value using binary chop.

##### A gibibyte-sized concrete example #####

As a concrete example, suppose we have a 1-gibibyte buffer stored in a
16-way B-tree whose leaves all happen to be 1024 bytes at the moment,
and we want to calculate what the line number is at a typical position
like byte 474,340,006.  Each lowest-level internal node embraces 16384
bytes; each node at the next level is 256 kibibytes; each node at the
next level is 4 mebibytes; at the next level, 64 mebibytes; and the
single top-level node is the whole gibibyte.

1. The first 7 64-mebibyte children of the root node are entirely
   before that position, and we can use a cached number of newlines
   stored in the root node for each of those children to add up the
   number of lines in the first 469,762,048 bytes of the file, leaving
   4,577,958 bytes.
2. Those bytes contain a single full 4-mebibyte block at the next
   level; we can add in its number of newlines, cached in its parent
   block, leaving 383,654 bytes over.
3. Those bytes contain a single full 256-kibibyte block at the next
   level; we can add in its number of newlines, cached in its parent
   block, leaving 121,510 bytes over.
4. Those bytes contain 7 full 16-kibibyte blocks at the next level; we
   can add in their numbers of newlines, cached in their common parent
   block, leaving 6822 bytes over.
5. Those bytes contain 6 full 1024-byte leafnodes; we can add in their
   numbers of newlines, cached in their common parent block, leaving
   678 bytes over.
6. Finally, we can iterate over those 678 bytes to count the newlines
   in them, and we have our answer.

So, in total, we had to add up 22 numbers, found in five blocks, and
examine 678 bytes of text, totaling about 1 us; and the worst case is
only about three times more operations, and the same number of random
memory accesses, so about the same time.  This is about four or five
orders of magnitude faster than just iterating over all the text.

If you insert or delete a newline in this buffer, you need to revise
five of those numbers.  You can alter the tradeoff slightly — for
example, within each node you can cache the prefix sums of its
children rather than their raw values, resulting in faster queries and
slower updates (worst case with tree height 5 and 16-way blocks, 5
reads and 80 updates), or you can use a binary-tree structure within
each block (worst case 20 reads and 20 updates).  But the number of
random memory accesses stays the same.

#### Monoidal incremental tokenization ####

It may not be obvious that syntax highlighting can be incrementally
handled in the same efficient way.  Syntax highlighting is typically
mostly a function of tokenization, which is typically regular except
in exceptional cases, such as here-documents in shell or Perl.
Regular expressions can be handled by an NFA; the elements of the
monoid in question are mappings from sets of NFA states to sets of NFA
states, and the monoidal operation is composition of such mappings.
Typically any block of text of more than a few hundred bytes has only
a few NFA states possible at its end, sometimes only one.

#### Monoidal incremental layout ####

Typically the column at which you display a character depends on the
font you're using, your wrap width, the kind of wrapping you're using
(character, word, or hyphenated, say), and the characters before it on
the (logical) line, which may be arbitrarily long.  As described in
[Monoid prefix sum](monoid-prefix-sum.md), you can efficiently compute
this incrementally in the same way.  (Occasionally it also depends on
the rest of the characters in the physical on-screen line, if you are
justifying, or the layout choices of the rest of the paragraph, if you
are doing some kind of TeX-like layout optimization.)

