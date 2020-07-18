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

Why ISAM rather than just a doubly-linked-list piece table?  You could
include memory pointers to the pieces in the marker objects instead of
ISAM keys.  Inserting and deleting into a doubly-linked list is easy;
you have to update all the markers concerned, but that is true with
ISAM as well.  So what is the advantage of ISAM here?

FP-persistence maybe?