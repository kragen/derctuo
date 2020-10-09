(From a discussion with Nick Johnson, though any errors are my
responsibility, not his.  And probably a lot of this is not novel,
being implicit in Okasaki at least.)

Bitcoin and similar systems include a hash of the previous block in
each new block, forming a chain of blocks.  But verifying that any
particular previous block in the block chain is truly an ancestor of
what you believe the tip block to be requires verifying all the blocks
in between.

(Often a more interesting question is whether a current candidate tip
block really does have the height it claims to have.  The approach
outlined below is not primarily concerned with that question, but it
does permit fast noninteractive probabilistic proofs of it.)

Merkle blockchain ropes and logarithmic-time appending
------------------------------------------------------

What we would ideally like is a Merkle *tree* of the previous blocks
instead of a linear Merkle block *chain*.  Both can be thought of as
Cedar’s “ropes”, which represent sequences as the leaf nodes of a
binary tree; in OCaml:

    type rope = Leaf of string | Fork of rope * rope

Or, more abstractly, a rope of some arbitrary type `'a` or α:

    type 'a rope = Leaf of 'a | Fork of 'a rope * 'a rope

Sometimes there's a third alternative:

    type 'a rope = Leaf of 'a | Fork of 'a rope * 'a rope | Empty

In these Merkle graphs, the pointers are realized as message digests
rather than memory addresses.  A chain is just the degenerate case
where the binary tree is pessimally balanced.  What we want is to
incrementally rebalance the tree as we append to the end of it.

If you have 8 leaf nodes to form into a perfectly balanced binary
tree, you need to construct 7 internal nodes (“forks”).  The first
fork can be added when you add the second leaf, but the second fork
cannot be added until you add the fourth leaf, at which point you can
also add the third fork.  Then the fifth leaf does not enable adding
any new forks, so it would have been okay to wait to add the third
fork until then.  The sixth leaf enables adding the fourth fork, the
seventh leaf does not enable adding any new forks, and the eighth leaf
makes it possible to add the fifth fork (over leaves 7 and 8), the
sixth fork (over forks 4 and 5), and the seventh fork (over forks 3
and 6).

You could think of these fork nodes as being an “index”, like that in
a relational database, of the block chain; but, instead of allowing
you to answer queries quickly, they allow you to construct proofs
quickly.

On average, you only need to add one fork for each leaf after the
first, but sometimes you cannot add it immediately.  So if you add at
most one fork with each new block (which adds a leaf), you will start
to fall behind.  But you fall behind only by a logarithmic amount; the
256th leaf enables the construction of 8 new forks, so at that point
the perfect binary tree will be 7 blocks delayed from the state of the
blockchain.

In essence each new fork consolidates the two previous smallest
remaining binary trees into a larger binary tree; the trees can be
held in a stack of fully compacted trees and a queue of possibly not
fully compacted trees.  Before adding a new leaf, we compact two trees
if possible, then add the leaf to the queue.  Here’s what the `stack;
queue` state looks like as we add the first 40 leafnodes, one at a
time:

    1: ; 1       11: 8 2; 1      21: 16 2 2; 1   31: 16 8 4 2; 1  
    2: 2;        12: 8 2 2;      22: 16 4; 1 1   32: 16 8 4 2 2;  
    3: 2; 1      13: 8 4; 1      23: 16 4 2; 1   33: 16 8 4 4; 1  
    4: 2 2;      14: 8 4 2;      24: 16 4 2 2;   34: 16 8 8; 1 1  
    5: 4; 1      15: 8 4 2; 1    25: 16 4 4; 1   35: 16 16; 1 1 1 
    6: 4 2;      16: 8 4 2 2;    26: 16 8; 1 1   36: 32; 1 1 1 1  
    7: 4 2; 1    17: 8 4 4; 1    27: 16 8 2; 1   37: 32 2; 1 1 1  
    8: 4 2 2;    18: 8 8; 1 1    28: 16 8 2 2;   38: 32 2 2; 1 1  
    9: 4 4; 1    19: 16; 1 1 1   29: 16 8 4; 1   39: 32 4; 1 1 1  
    10: 8; 1 1   20: 16 2; 1 1   30: 16 8 4 2;   40: 32 4 2; 1 1  

The new leaf can embed the fork within it, thus “signing” the fork at
insignificant extra cost.  The pointers in the fork can be the hashes
of the two blocks containing its child nodes, annotated with bits to
indicate whether the pointers are leaf pointers or fork pointers.  You
might think that when the fork is included, this eliminates the need
to include the hash of the previous block in the new block, because
the previous block can be reached by following the right-child
pointers down the tree of forks; but in fact the new fork might not
include the previous block.  So you still need the previous block
pointer.

Moreover, to permit efficient traversal of the trees, each fork also
needs to include a pointer to the previous fork on the stack, if any.
In the above example, the fork containing the first 16 leaves is
created with the 19th leafnode, but is not merged into a 32-leaf fork
until leafnode 36.  When you’re looking at leafnode 35, how are you
going to find the older 16-node fork?  You need a pointer back to leaf
19.

From a rope perspective, your sequence of blocks is a concatenation
node of the stack and a queue; the stack is either empty or a
concatenation of a stack of all the balanced trees before the last,
and the last balanced tree; each balanced tree is either a leafnode or
a concatenation of two balanced trees; and a queue is either empty or
a concatenation of a queue and a leafnode

From the rope perspective, this structure is just a rope; the
distinctions between the state, stacks, queues, and balanced trees can
be entirely implicit in the structure.  A state is a fork of a stack
and a queue; a stack is empty or a fork of a stack and a balanced; a
balanced is either a leafnode or a fork of two balanceds; a queue is
empty or a fork of a queue and a leafnode.  So the “type” of each fork
(state, stack, queue, or balanced) is encoded by its parent’s type and
which side it’s on.

Each new block encodes, in a sense, a new balanced and a new
stack with it as the right child and a new queue with a new right
child (which is the block itself).  The only funny business is that,
as I’ve described the queue above, consuming items from the front of
the queue requires reconstructing all the forks inside the queue, and
these forks are not recorded at all in the blockchain.

The length of the queue is bounded by the height of the tree, so it’s
only logarithmic.  With an ephemeral data structure (rather than an
FP-persistent structure like an orthodox rope) you could do the queue
operations in constant time, making the whole node-append operation
constant-time.  This isn’t important for block-chain applications, but
it could be useful in other rope applications.

However, although *appending* to the structure can be thus made
constant-time, it still takes logarithmic time to *query* it, which is
usually more common, so I think logarithmic time for appending will
almost always be good enough.

### Code for the table ###

The data for the above table was produced by the following code; I
then formatted it into columns:

    s, q = [], []

    for _ in range(40):
        if len(s) > 1 and s[-1] == s[-2]:
            s[-2:] = [s[-1] + s[-2]]
            q.append(1)
        else:
            q.append(1)
            if len(q) > 1:
                n = q.pop(0)
                n += q.pop(0)
                s.append(n)
        print('{}:'.format(sum(s) + sum(q)),
              ' '.join(str(i) for i in s) + ';', ' '.join(str(i) for i in q))

Probabilistic chainheight validation
------------------------------------

As for the probabilistic proof mentioned earlier, if you annotate each
fork with the total amount of hashing (PoW) work in its leafnodes,
then you can choose a random leaf node to which to validate the path
down from the current state, for example in proportion to the amount
of hashing it is claimed to represent.  If you’ve been fed a fake
blockchain that pretends to represent 10% more work than it really
does, then you’ll have a 10% chance of finding a discrepancy, say
where a fork’s work total isn’t the sum of its children’s work totals,
or where the previous node in the tree isn’t actually the predecessor
it specifies.  This assumes the thief can’t predict which random node
you’ll try to validate.

By validating several randomly chosen leafnodes this way, each in
logarithmic time, you can achieve an arbitrarily high confidence level
that the whole blockchain is valid, as it claims to be.  For example,
after validating 44 random leafnodes in this way, you would have a 99%
chance of finding one that was in the faked 10%, I think.  If only 1%
was faked, you would need to check 459 random leafnodes to have 99%
chance of detecting the fraud.

But this is a fairly small cost.  Suppose there are 8 million
leafnodes, so you may need to go through as many as 44 forks on your
way down to a leaf; if all you have is a sequential file of blocks and
an index of it by hash, this could take 87 random accesses, about a
second on spinning rust with 8-ms seek times.  So you could finish the
459-leafnode probabilistic validation in under ten minutes.

Less pessimistically, you could arrange the 8 million forks into a
B-tree; each consists of, say, a 32-byte hash for each of its two
children, 64 bytes in total.  On spinning rust, you might use a
524288-byte treenode size, which can holds 8192 forks, really 8191: 13
levels of the tree.  The up to 23 stack items and the up to 23 queue
items can be held in RAM.  So validating each leafnode involves
reading two B-tree blocks and the leafnode, 3 random accesses rather
than 87, plus checking the hashes.  So you can finish all 459
validations in 10 seconds rather than 10 minutes.

On SSD you can use 16384-byte B-tree nodes — 255 forks each, 8 levels
of tree — and access them in 100 μs each.  So traversing 23 levels of
the binary tree requires traversing only 3 levels of the B-tree plus a
leafnode, 400 μs, so your access time for the 459 leafnode validations
is 180 milliseconds.

(Hmm, actually I realize I didn’t include the chainheight annotation
in the sizes of the forks, but the difference is not very large.)

Application to LSM-trees
------------------------

Much of the logic above isn’t concerned with precisely what operation
we do to merge together two trees into a larger tree.  In the above,
it was simply a matter of constructing a new fork with the two trees
as children, a constant-time and constant-space operation.  But it’s
all highly suggestive of Lucene’s merge-based approach, also used in
LevelDB, nowadays called a “log-structured merge tree”.  In an LSM
tree, when you merge two 16-item segments into a 32-item segment, you
have to read through each of them sequentially in order to sort them
into order by key, perhaps constructing some kind of skip file or
index to enable rapid random access by key.

The amount of work becomes smaller if we use N-way merges: instead of
first merging 1+1 = 2, then 2+2 = 4, then 4+4 = 8, then 8+8 = 16, then
16+16 = 32, which involves appending an item to a new segment 63
times, we directly merge 1 + 2 + 4 + 8 + 16 = 32, thus only doing it
32 times.  We can also exchange some more variability in query time
for a lower index construction time, by delaying merges for a longer
time, say until the new segment will be 4× or 8× larger than the
largest old segment being merged, not just 2× as in the examples
above.

The larger point, though, is that LSM-trees fundamentally involve more
work per item than just concatenating them into ropes.  But it’s only
logarithmically more work, and we can spread it evenly over the time
when items are added in an analogous way.  The temporal sequence won’t
be exactly the same: generating the 32-item merged node, done
atomically at block 36 in the above example, will take 16 times as
much work as generating the 2-item merged nodes, which in the above
example are created at blocks, 2, 6, 37, etc.

So we will generate these larger merged nodes gradually, over the
course of adding several leafnodes.  And I think the amount of work we
do per leafnode will gradually increase, but only logarithmically.  I
haven’t worked out the reality yet.  Each new leafnode might include
some logarithmically large set of pointers to segments it's merging
and cursor positions in them, and a chunk of merged data.

What does this have to do with blockchains?  Well, you could plausibly
collectively generate a queryable database index in this way as part
of a blockchain, but efficiency is in some sense not the point of a
blockchain — inefficiency is.  A blockchain is designed to make it
infeasibly inefficient to violate the established consensus rules on
who owns what.

Outside of blockchains, I suspect that LevelDB does in fact work this
way in order to avoid unbounded pauses when inserting and deleting.
