Skip lists are generalized sorted linked lists that incorporate
logarithmic-time insertion, deletion, and search.  In their normal
form, the link pointers point in only one direction, let's say
forward.  The generalization is that each node has a "height", a
positive integer chosen randomly from an exponential distribution,
which tells *how many pointers* it will contain.  The height-1
pointers are the usual linked-list pointers, but the other pointers
skip nodes; a height-P pointer, instead of pointing to the next node,
points to the next node *of height P or greater*.

Skip lists are transposed, randomized B-trees.  You take a B-tree,
replace all of its adjacency relationships within B-tree nodes with a
previous or next pointer — turning the node into a doubly-linked
list — and then replace all child pointers with new adjacency
relationships.  (You can treat the child pointers as going to the
first piece of the split-up child node, or the last — it doesn't
matter, as long as the other pieces are reachable from it.)  Now you
have a skip list, but an unorthodox one, with fairly regular spacing
between nodes of the same height.

The B-tree invariant that each node has between N and M keys
translates into a new invariant in these lists: if you can reach a
previous node by following a single pointer at height P, then it will
take between N and M hops by following a single pointer at height P -
1.

You can do the usual operations of insertion and deletion, including
appending, on such a list without violating its invariants.  Whenever
you create a node containing a pointer at height P - 1, you must check
to see how many nodes surround it that do not contain a pointer at
height P.  If there aren't enough, you need to extend the node to
height P, then repeat the process.

(It is by no means clear that this is superior to the standard
approach of picking a random height for each new node, but it gives a
one-to-one mapping to B-trees maintaining their usual invariants.
Clearly you can use the skip-list random-level approach with the
B-tree layout as well.)

A somewhat inconvenient aspect of the standard skip-list
implementation is that the nodes are many different sizes, which is
more difficult for dynamic memory allocation (and some type systems)
to handle.  It's reasonable to ask if there's a way to avoid this.

If the distribution of heights is 50% 1, 25% 2, 12½% 3, and so on,
then the mean height will be 2.  You could maybe provide 2 link fields
per node, which sounds like... a binary search tree!

So here's an idea.  What if you assign heights as before, but only
have two pointers per node: one to the immediately next node, the
other to the next node *of a larger height than the immediately next
node*?  (I also thought of "of a larger height than the current node,"
and "preceding a node of a larger height than the immediately next
node" and such things.)  This ought to permit rapid traversal in
essentially the same manner as an ordinary skip list.

XXX try it

Clearly you can also just sort of bulldoze the pointers of an ordinary
skip list across the following nodes, so a node of height 4 will have
a heigh-4 pointer, then be followed with a node with a height-3
pointer, then one with a height-2 pointer, and then if either of those
nodes had a height of more than 1 themselves, then the corresponding
pointer will be pushed onto the next node.  The difficulty with this
variant is that insertion and deletion is no longer cheap.