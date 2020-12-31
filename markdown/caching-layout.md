Let’s consider building up a GUI with table layout, flowable text,
graphical objects, and clipping, based on a pipeline from some
arbitrary state data, through to a set of nested boxes, to a layout
which assigns positions to all these boxes, to pixels to put onto the
screen, all in a form that is purely functional and therefore
tractable to memoize or cache, without losing unreasonable amounts of
efficiency.

First, from the arbitrary state data, we build up a structure of
nested boxes.  Maybe we iterate over the characters of a text and
generate letters from a font, which we stack into hboxes that form
words, which we fill into paragraphs which will compute a reasonable
set of positions for the word boxes within the resulting paragraph
box, which is perhaps a child of a vbox.  Maybe somewhere there are
some GUI elements that get laid out in a table, and one of them is a
dropdown list which is associated with a transient dropdown, which
should get composited on top of the whole shebang at a later step.
Maybe all of the above is within a scrolling pane of a larger window.

If this stage of the process is comprehensively memoized, then rather
than a tree of nested boxes we will have a DAG of nested boxes;
perhaps the letter “f” from a given font only results in a single tree
node.  Also, if it’s comprehensively purely functional, it’s safe to
discard any of these boxes as well, as long as we save the function
call that produced them so that we can restart it if we need the box
again.

Second, to do the layout, we do three further passes over this DAG of
nested boxes.

Pass 2.1 is to compute a corresponding DAG of nested boxes augmented
with requested sizes and elasticities.  This is intended to be a
mostly bottom-up pass, where each resulting node depends on its
descendants, but not on the space actually available for it on the
screen.  (This is easier for Tk-style widgets than for paragraphs!)

Pass 2.2 is to compute the actual positions and sizes of each nested
box, which is a top-down process, beginning with the size assigned to
the root box.  This produces another DAG of nested boxes augmented
with (x, y, width, height) geometry information.  Each box is given
only the width and height assigned to it, and then can follow any
policy it likes to assign positions to some or none of its children
within itself, whatever is visible.  The positions may overlap, in
which case the z-order is important.

Pass 2.3 is to add floating boxes by iterating over the tree from the
previous pass and giving each visible box an opportunity to produce
things such as dropdown lists that should be propagated further up the
tree.

Now that we have a layout, we have essentially a scene graph, and we
need to rasterize the scene graph.  So we make two more passes.

In pass 3.1, we produce a new augmented tree (this is starting to
sound like a nanopass compiler framework) that has a pixel buffer
associated with each box, a texture buffer which is scaled to the
box’s visible area, and which contains the graphics from the
*background* of that box, not the children.  But the node still has
references to its child boxes and their positions relative to it.
Some boxes may be translucent, but most are opaque.

Pass 3.2 is rasterizing the actual screen.  The standard scan-line
rendering algorithm (Wylie et al. 1968) is, I think, to sort all the
objects on the screen by their minimum Y-coordinate, then iterate over
the scan lines maintaining a currently-visible priority queue (the
so-called “active edge table”) of the *edges of* the objects
intersecting the current scan line according to their *maximum*
Y-coordinate, while adding the edges of new objects to the queue when
we get to their minimum Y-coordinate.  (The original Wylie et
al. algorithm calculated an “occupied table” of *polygons* for each
scan line.)  Within each scan line, we sort the edges of the visible
objects by X-coordinate (an insertion sort which does work
proportional to the number of new out-of-place edges, times the total
number of edges), then iterate over blocks of pixels to sample from
the textures of the topmost object in that block, and whatever other
objects are visible through it, if alpha-blending is called for.
Finding out the topmost object at a given pixel is also a
priority-queue problem, this time with Z-order instead of
bottom-order.

Unlike all the previous passes, this last pass is not cacheable at a
node-by-node level like the others because its results aren’t
associated with subtrees; a box may be overlapped by some other box
that isn’t its descendant, and we respond by not trying to sample its
texture in the overlapped area.  It can make up for this by being fast
so we don’t need to cache it; remember, it’s just sampling from
textures, typically one or two samples per pixel, if most boxes really
are opaque.

If we want to do this in a real-time system that guarantees
responsiveness, we need some kind of cheat for cases where we can’t
meet our deadline with the correct answer.  If there are too many
things on the screen to rasterize in time, for example, we could
rasterize only certain scanlines, for example, or make everything
opaque, or only rasterize the first N objects on each scanline, or the
first N objects after the largest X-coordinate that was reached during
the last frame.  If it’s instead one of the tree stages that’s taking
too long, we might be able to use an old or outdated version of the
subtrees we don’t have time for, perhaps tagging them in order to gray
them out.

To figure out what’s worth caching, we might be able to use recency,
reference counts on cacheable nodes, and the computation time they
took; when we discard a cacheable node, we must move the computation
time it took to each of its previous parent nodes.

Some ingenuity may be required for the cache manager to do this
quickly and optimally; a straightforward suboptimal solution is to
maintain two caches, add each newly memoized item to both caches, and
whenever a cache becomes full, empty it in constant time (by resetting
an arena pointer); additionally, whenever a cache crosses the
halfway-full mark, empty the other cache unless it's nearly full.
Lookups need not consult both caches, since one is a perfect superset
of the other, but should promote the cached item to both caches.

The result of this approach should be to empty the caches alternately
at nearly regular intervals, so that all the items that are referenced
more often than that interval remain always cached, and items that are
referenced less often have some probability of remaining cached.  In
particular, if a parent call is getting reliably cached, then its
children and grandchildren will not get referenced, and will be
eventually eliminated from the cache, which is in some sense optimal.
It may lead to bad worst-case behavior, though.
