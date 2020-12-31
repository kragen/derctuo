The bent-wire locking toggle used to close the tops of some bottles is
a beautiful little piece of mechanical design, and CNC wire benders
can produce such devices at high speeds and high precision.  And
bending wire manually is often expedient and relatively easy.
Galvanized mild steel wire is widely available at very low cost, and
rusty mild steel wire is constantly discarded by the side of the road.
And in both US English and Argentine Spanish, there’s a common figure
of speech for an expedient, low-quality solution that refers to it:
“duct tape and baling wire” and “atar con alambre”, respectively.

Straightening with two plates
-----------------------------

You can straighten a piece of wire by rolling it back and forth
between two flat surfaces after approximate straightening; it’s
helpful to feed it in incrementally so that you’re only straightening
a little bit at once.  I’ve used a slab of granite and the back of a
scrap ceramic floor tile in this way, getting bent-up wire to
sub-diameter deviations from straightness over short lengths.  It’s
easier if your wire is round rather than needing to be twisted to
approximate roundness.

Work-hardening and annealing
----------------------------

Mild steel can’t be hardened martensitically, but it can experience
considerable work-hardening, and this plays a significant role in
shaping things from mild-steel wire by hand, for example with
needlenose pliers.

It’s usually a difficulty: once you bend some wire, you ain’t never
gonna unbend it, because the place you bent is harder than the rest of
the wire.  But if you buy some wire, it doesn’t come straight.  It’s
coiled, if you’re lucky.  If you grab discarded rusty wire off the
street, it’s gonna be bent all to hell, and that’s going to introduce
some unpredictability.

CNC wire-bending machines deal with this problem by bending the wire
back and forth in all directions with rollers, in order to straighten
it and harden it uniformly before they shape it.  This way all the
wire is hardened to a greater extent than the kinks were beforehand.
Even if you can do this by hand, you may regret it, because the
uniformly hardened wire is uniformly harder to bend, too.

Maybe a better approach is to anneal the wire.  Annealing steel
properly is a pain in the ass.  You [need to austenitize it][2], which
[takes temperatures ranging from 738° to 900° for mild steels][1],
with the highest temperature being pure iron; in itself that’s not too
hard, but then the recommended cooling rate is about 11 mK/s, or
11°/kilosecond.  Not only does this take all day, it also demands a
degree of temperature control well beyond the capability of a stove
burner or butane torch, which can easily heat the wire up to 1000°,
high enough to anneal steel, but probably can’t cool it down any
slower than about 1°/s, 90 times faster.

[1]: http://threeplanes.net/toolsteel.html
[2]: https://en.wikipedia.org/wiki/Annealing_(metallurgy)

However, mild steel is less demanding than quench-hardenable steels in
its annealing requirements, and I think you can get substantial
softening at these much higher cooling rates; the literature seems to
claim that anything from 5°/s to 200°/s should be just fine.  I took a
straightened wire and heated two spots on it on the stove burner, one
to orange heat and the other a bit below, for 20 minutes, then let it
cool over the course of about a minute, so on the order of 20° to 50°
per second.  Subsequent bending happened preferentially at the
annealed spots, showing that they were softer than the rest of the
wire.

Work hardening is not all bad, though.  When you bend wire, it bends
into a smooth curve instead of kinking like a drinking straw because
the parts that have bent least are the softest.  (In a drinking straw,
or in a collapsible tube in general, the parts that have bent most are
the softest, which is why they bend even more, forming the kink.)
And, by twisting two wires around one another, we can make them
dramatically harder, in the sense of having a much higher yield
stress, though no stiffer elastically.

Creepage and nicking
--------------------

Design for bending wire, like design for bending sheet metal, requires
a creepage allowance; tight bends are not ideal points, but curves.
Tighter and more precisely placed bends can be achieved, at the
expense of strength and stiffness, by nicking the wire at the desired
bend location.  This permits bends whose radius is less than a single
wire diameter.  However, imprecision in nicking (I’m using needlenose
pliers) can result in either a failed bend or a cut wire.  This takes
about a joule with these needlenose pliers and this baling wire I have
here, which is about 1.7 mm in diameter; but it depends on the shape
of the nicking dies (these have about a 90° dihedral included angle)
and on the hardness of the wire.  I measured this energy by dropping a
known weight of 827 g from a known height of 100 mm onto the handles
of the needlenose pliers.

Compliance
----------

Wire is very springy.  Although baling wire is much less springy than
music wire or other hardened steels — both in the sense of elongating
less before beginning plastic deformation, and in the sense of having
lower moduli of elasticity — it is still capable of storing enormously
more energy elastically per volume than other everyday materials like
wood, fired clay, glass, paper, cardboard, or PET.  (Rubber beats it,
though.)

As a result, to an enormous extent, you can change the compliance of a
wire structure by changing its geometry.  If you can use an
arbitrarily large amount of wire, you can get an enormous but not
arbitrarily large amount of compliance into a given space.  (If you
can make the wire arbitrarily thin, you can get to an arbitrarily
large amount of compliance.)

In the opposite direction, achieving high rigidity is more difficult.
To some extent you can make progress with triangulated structures made
of short struts, and wires as straight as you can get them; but
connecting wires together with high rigidity without welding is very
difficult.  Twisting two wires together increases the thickness of a
strut, but at the same time it turns each wire into a helix, which is
less rigid.

When two or more wires are twisted together, they spring back when you
stop twisting, loosening their grip on one another.  Thereafter they
are connected with a screw joint with substantial backlash.  I think
it’s possible to take the backlash out; the usual way is to preload
two coaxial screw joints with a spring under axial compression, though
tension works just as well.  I haven’t tried this with twisted wire
yet, but I suspect it will be difficult.

Wireframe design as graph traversal
-----------------------------------

Suppose you want to make a regular rhombic dodecahedron of a given
size with a minimal amount of wire and no unnecessary cuts, outlining
its edges in wire.  By Euler’s theorem about the bridges of Königsberg
(pbuh), you cannot do it with no cuts and no repeated edge traversals;
for all that the rhombic dodecahedron has 6 vertices of degree 4, it
also has 8 vertices of degree 3, and each of those vertices requires
either the beginning or ending of a wire, or a double traversal of an
edge, for example by twisting two wires together.

So, one way to do it is to build the shape from 4 wires, each starting
and ending at one of the degree-3 vertices.

But suppose instead we duplicate one of the edges out of these
degree-3 vertices, representing our double traversal of an edge.  This
converts it into a degree-4 vertex, but the duplicated edge connects
at the other end to one of the old degree-4 vertices, which now
becomes a degree-5 vertex — so far we are no better off than before!
But that vertex is exclusively connected to (old) degree-3 vertices,
so we can simply duplicate one of its edges to one of them, converting
what was originally a degree-4 vertex into a degree-6 vertex, and
eliminating the other degree-3 vertex.

Now we have only 6 odd-degree vertices.  By repeating this process
twice more we can reduce it to 2, where the wire is free to begin and
end, curled around the kink between two more edges; and our wireframe
is complete.

A similar process can be done with wireframes in general, but it
clearly demonstrates the difficulties introduced by odd-degree
vertices in hand-twisted baling wire.  If you are designing a
wireframe *de novo* rather than using one Archimedes thought up, you
may prefer to avoid odd vertices entirely.  For example, if you create
a so-called “geodesic dome” by subdividing the triangles of an
octahedron rather than the traditional icosahedron, you will have
greater inequality in strut lengths, but no odd vertices.  The
curvature will be provided ultimately by degree-4 vertices rather than
degree-3 or degree-5 vertices.

You might think that an alternative basis for subdivision may be the
cuboctahedron, the polyhedral dual of the rhombic dodecahedron, since
all its vertices are degree-4; but, by the time you eliminate its
square faces as an offense against omnitriangulation, you are back to
having a subdivided octahedron.  It’s a distinction without a
difference.

Dimensional stability
---------------------

Even baling wire is fairly dimensionally stable, due to steel’s
temperature coefficient of expansion of some 12 ppm/°, compared to
most other common materials.  Brick and glass (even soda-lime) exceed
its dimensional stability, as does the lengthwise dimension of wood.

Kinematic pairs and flexures
----------------------------

It’s easy enough to twist some baling wire into a tight helix that
another piece of wire can fit through, forming a cylindrical joint.
But its translational motion is highly unsatisfactory, due to the
usual problems of translational motion in kinematic pairs between
rigid bodies, but exacerbated by the compliance of the wire.  As a
revolute joint it works better, but it’s somewhat prone to unwanted
translational motion.  I think it’s usually possible to overcome this,
but it takes some doing.

In many cases, though, I think a flexure design may be more suitable
for the characteristics of the wire.  Clothespin-like spring clips
bent from a single piece of wire, with or without a helical flexural
pivot, are well-known, and of course the paper clip is a flexural
bent-wire machine ubiquitous throughout the modern world.

Ends and burrs
--------------

Snipped wire ends have sharp burrs on them.  Deburring is an option.
With needlenose pliers, though, it is generally easier to curl them
into a tiny circle so that these burrs are up against the edge of the
wire a few millimeters away.  This is enough for many purposes.

Paperclips use smoothly curved radii to grab paper without tearing it,
a potential problem despite their neatly deburred ends.
