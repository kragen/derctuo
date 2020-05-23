Laser-cut finger joints are a popular way of joining MDF into boxes.
The fingers on each side of the joint are cut slightly wider than the
spaces on the other side, because otherwise there would be slop around
them twice the width of the kerf, which is on the order of 100 μm, and
the joints would not join without glue.

This means that you cannot cut both sides of the shape from the same
piece of fiberboard with a single zig-zag-zug sort of cut; you’d have
that slop.  So you apparently need to cut the two sides of the joint
separately, doubling the cut time and the cost.

But do you?  Suppose each finger and each space along the joint is
250 μm narrower than the preceding finger or space on that side, and
you use a single zig-zag-zug.  Then the pieces won’t join together at
the position they were cut out — but if you shift them by a single
finger–space cycle, they will join firmly, with 25 μm of interference
on each side.  So if you shift the two pieces by a short distance
relative to each other in the to-be-cut layout, and shift them back to
assemble them, they will fit together snugly.

So, for example, you might have a 150-mm-long finger joint made of ten
15-mm-wide fingers, and you can overlap 90 mm of it in this way: 30 mm
at the bottom (two fingers, one on each side) is just the left piece,
90 mm (six fingers) in the middle is overlapped, and 30 mm at the top
is just the right piece.  The finger widths vary from 31.25 mm at the
bottom to 28.75 mm at the top.  This results in 180 mm of cutting,
plus 11*x*, where *x* is the thickness of the material — 33 mm if it’s
3-mm MDF.

This offset or stagger might make efficient nesting more difficult,
and thus increase material costs, or it might not.  But, with MDF,
cutting costs greatly exceed material costs.

An alternative to edge finger joints is mortise-and-tenon joints.
These can be tight, like finger joints, but they can also be loose,
with an extra 100 μm or so of slop deliberately left to ensure that
pieces can slot together easily.  A series of such mortise-and-tenon
joints substitutes for a dado or groove joint, which cannot be
themselves made by sheet-cutting technologies like laser-cutting.
Using a sequence of such loose mortise-and-tenon joints rather than a
finger joint both eases assembly and also reduces the chance that an
out-of-tolerance cut will convert a tight fit into an impossible fit.
A few SIGGRAPH papers have shown ways of designing arbitrary
assemblies so that all the pieces slide into place with such joints,
locking previous pieces into place; a final piece with an interference
fit is adequate to hold the whole assembly together.

A different way to get finger joints to have interference fits,
without staggering them, is to angle the edges of the fingers rather
than using right angles, so the finger tips are narrower than the
finger bases, and the spaces between the fingers are narrowest at
their base.  A difference of 120 μm over a 3 mm finger length works
out to about a 92.3° angle rather than the 90° usually used.  I
haven’t tried this, but I suspect that the resulting joints will not
be as strong as regular finger joints, because only about one fourth
as much area is in contact, but they should be easier to assemble and
fairly robust to process variation.
