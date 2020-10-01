Wang tiles are one of the simplest Turing-complete systems.  You have
some set of square tiles of a single size and the task of tiling, say,
the infinite two-dimensional plane with them; the restriction that
makes this difficult is that the tile edges are colored with some
finite set of colors, and the colors of the adjacent edges of
contacting tiles must match.  In Hao Wang’s original proposal the
tiles were forbidden to rotate.  It’s straighforward to see how you
can translate, say, binary addition or a Turing machine into this
formalism; moreover it can be deterministic or nondeterministic.

This is a handy way to generate things like random game boards, but I
was thinking of a different application.

What if each tile type is a type of molecule?  Molecules can be highly
selective about what kind of reaction sites they bind to, and modern
organic chemistry is able to perform quite sophisticated syntheses.
You can of course have molecules that bind together in three
dimensions rather than two, or rotate, but that additional power is
not necessary in this case, as long as you can keep them from glomming
together in undesired ways too much.  (It might improve efficiency,
though.)

This could allow you to self-assemble a massively parallel computation
on a solid substrate.  It might be desirable to use only one molecule
type at a time to keep them from binding together in the solution
rather than on the substrate.  This sequence of reagents itself can
constitute an input stream being processed massively in parallel, but
for the basic Wang-tile abstraction, you just need to cycle through
all the reagents repeatedly enough times.
