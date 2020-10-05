Globoflexia, or balloon twisting, is a popular form of entertainment,
especially for children; a skilled balloon twister can make an
evocative, if cartoonish, sculpture of an animal or person within a
few seconds to a minute.  Extremely elaborate sculptures are feasible
over a few hours; because the material is so light, getting all of its
compressive strength from air, sculptors can easily build and
manipulate sculptures far larger than themselves.  Because the
balloons leak, the sculptures are ephemeral, lasting at most a few
days.

Much to my surprise, there's a world globoflexia conference every two
years at which teams from dozens of countries compete.

What if you could use globoflexia as a medium of expression for more
permanent ideas?  Obviously you can photograph the sculptures, thus
making images or videos of them, but the underlying three-dimensional
form of the object is lost.

So I've been thinking about three different ways to do this:
photogrammetry, spray foam, and papier-mâché.

Photogrammetry
--------------

If you can 3-D scan balloon sculptures into a computer, you can use
them as a means for telling the computer what to do; this could be, as
in Dynamicland, a real-time interactive process of shaping
computations with your hands, with real-time projected feedback, or it
could be more a kind of batch data-entry thing, for example for
designing three-dimensional shapes for later tweaking and automated
fabrication, whether at the same scale, a larger scale, or a smaller
scale.

Existing photogrammetry methods do not work for balloons.  But the
balloons in question are not inherently algorithmically difficult:
each is a well-controlled solid color, displaying gradients of
intensity corresponding to local degree of stretch and illumination,
with well-controlled specular highlights.  The images generally only
contain edges at the edges of the balloon silhouette, at wrinkles
around twists, around specular highlights, and outside the balloons.
These highlights give a fairly precise read on the surface angle and
curvature at a particular point, as do the silhouette edges.

Moreover the balloon sculptures' shapes are themselves well-behaved:
the surface at most points has a relatively smooth curvature
determined mostly by the gauge pressure and the tensions in two
directions.  Rubber's complex pseudoelastic thermodynamic behavior is
not so complex as to make this a very difficult problem.

Further information can be obtained by looking at the balloon
sculpture from different angles, as is normally done in
photogrammetry, thus scanning the specular highlights and silhouette
contours over the surface.

Given this information, it remains to optimize a model of the balloon
sculpture to account for the observed photos as parsimoniously as
possible, using standard methods like finite element analysis,
Markov-chain Monte Carlo, gradient descent, and genetic algorithms.

Spray foam
----------

What if you fill the balloons with a hardening foam instead of air?

Conventional polyurethane expanding spray foam insulation has been
available for decades.  You spray it as a thixotropic liquid foam,
which accommodates itself to the container it's in before slowly
polymerizing into a light, hard, thermally and electrically insulating
foam with substantial mechanical strength.  Some formulations form
waterproof closed-cell foams, while others form lighter-weight
open-cell foams.  There are formulations that ship as pairs of liquids
to be mixed in a gun, for high-volume applications, and other
formulations that you just squirt out of a can.

The materials that form polyurethane foams are fairly reactive until
they've finished forming the foam, and that may be a fatal flaw for
squirting them into rubber balloons: they may corrode the balloons and
pop them before the foam has hardened.

Polyurethane is not the only possible hardening foam.  Latex foam is
widely used for pillows, mattresses, and theater special-effects
makeup ("prosthetics"), in which last use it is typically cast in
molds before curing.  Protein foam is a popular dessert, both as
meringue (with air whipped into it) and as gelatin foam in so-called
"molecular gastronomy" or "modernist cuisine", where the gelatin gel
is mixed with nitrous oxide under high pressure and low temperature,
like canned whipped cream.  Gelatin foam is also widely used for
makeup, sometimes whipped like meringue, but sometimes foamed with
non-double-acting baking powder (for example baking soda with cream of
tartar) or even yeast.

There's also been a lot of work in recent years on foamed concrete,
sometimes called "aircrete".  This consists of portland cement, water,
a surfactant (Suave shampoo is reputed to work well, though there are
also specific surfactant mixes from companies like Drexel), possibly
some foam stabilizers (I suspect gelatin might work well for this),
and a great deal of gas.  Sometimes sand is used, but rocks are never
used.  The original 1920s process for foaming concrete ("autoclaved
aerated concrete") used aluminum powder mixed into the concrete mix.
After molding the concrete was heated in an autoclave to react the
aluminum with some of the lime in the concrete, thus foaming the
concrete with hydrogen gas, as well as accelerating the formation of
the calcium silicate hydrates that bond the concrete.  The more common
method nowadays is more like meringue: air is mechanically mixed into
some of the water before mixing in the wetted cement.

You probably can't foam any traditional concrete with anything similar
to baking powder, because the strongly basic nature of portland
cement, lime, Sorel cement, and refractory calcium aluminate will
destroy the baking-powder acid without producing any gas.
Non-traditional concrete binders like low-alkalinity waterglass or
molten sulfur might be less corrosive, but probably also are not a
realistic way to fill balloons.  And I suspect that at room
temperature aluminum powder will not produce hydrogen fast enough.

The strongly basic nature of these cements might also cause them to
attack the balloons.

Most resins can be foamed in a way similar to polyurethane spray foam.
Radio-controlled airplane hobbyists commonly mix a secret "foaming
agent" from R&G into two-component epoxy to get an epoxy foam, for
example.  One publication on the use of polysilazane for this purpose
suggests that powdered aluminum mixed with soda lye is the usual
foaming agent!

Whatever foam is chosen, whether one of the above or something else,
the idea is simply to fill the balloons with the foam or incipient
foam rather than just air.  Then you twist the balloons into the right
shape, carrying the foam along with them, and leave them there until
the foam has hardened.  You may want to spray some kind of adhesive
onto the joints, since otherwise the foam in the different segments of
the balloons will only be connected together through the balloon
rubber, which may not be very stable.

Papier-Mâché
------------

An alternative, and possibly complementary, approach is to put
something on the *outside* of the balloons that hardens there, forming
a hollow, tubular, continuous version of the shape you have made with
the balloons.  The traditional material for this is strips of paper
dipped in wheat paste, but there are many possible variations on the
papier-mâché theme.

In addition to wrapping the balloons tightly, you can use the balloons
themselves merely to form a frame over which sheets of adhesive-soaked
fiber reinforcement are draped.

For the adhesive, rather than wheat paste, you could use:

- PVA glue;
- epoxy resin, if it doesn't attack the balloons;
- plaster of Paris (thank you, Javier Candeira!);
- sodium silicate waterglass;
- slaked lime, if it doesn't attack the balloons or fibers;
- portland cement, if it doesn't attack the balloons or fibers and the
  color isn't a problem;
- calcium aluminate cement, if it doesn't attack the balloons or
  fibers, and refractoriness is desired;
- Sorel cement, if it doesn't attack the balloons or fibers, and
  maximal strength is desired;
- latex paint;
- shellac;
- polyurethanes;
- urea-formaldehyde resin;
- phenolic resin;
- various kinds of solvent-based plastic cements such as PVC dissolved
  in acetone, if the solvent doesn't attack the balloons or fibers;
- clay, whether simply allowed to dry or later fired;
- tar, though probably using a solvent rather than heat;
- paraffin or other waxes, if the balloons can handle their melting
  temperatures;
- other adhesives;
- some mix of the above.

For the fiber reinforcement, instead of paper, you could use:

- heavily perforated paper;
- paper towels;
- cotton cloth, whether light like cotton tulle or heavy like canvas,
  and whether with a narrow weave like twill to maximize the strength
  of the fabric or a loose weave to ensure good adhesion between the
  adhesive on both sides of the fiber;
- burlap (aka Hessian), especially sisal or jute, to minimize cost and
  ensure good adhesion between the adhesive on both sides of the
  fiber;
- gauze, as in traditional plaster casts for broken bones (thank you,
  Javier Candeira!);
- mosquito netting;
- fiberglass cloth, as in traditional glass-reinforced polymer layups
  or in printed circuit boards, although if the binder is strongly
  basic you might need to use alkali-resistant fiberglass;
- carbon fiber;
- ceramic fiber like those used in refractory blankets and flocking,
  if resistance to high temperatures is desired (typically these
  fibers are mixes of mullite, alumina, zirconia, and silica);
- basalt fiber;
- steel window screens;
- aluminum window screens, if the binder is not strongly basic;
- stainless steel cloth;
- copper wires;
- gel-spun ultra-high-molecular-weight polyethylene fibers;
- other fibers;
- some mix of the above.

If the adhesive is less flexible than the fiber reinforcement (e.g.,
has a higher Young's modulus), then the fiber reinforcement may just
weaken the binder instead of strengthening it, although it can produce
some "strain hardening" behavior where the adhesive cracks but the
fiber keeps the adhesive cracks from opening wider and thus continuing
to propagate.  Still, even weak fibers can hold the adhesive in
position until it sets, and for some purposes the adhesive alone will
be strong enough without any help from the "reinforcement".

It may be worthwhile to also include other additives in the adhesive,
whether inert fillers or reactive; for example:

- pigments;
- quartz sand for extra strength;
- olivine or zircon sand for extra strength at higher temperatures;
- clay, such as bentonite, functionalized if necessary to bond well
  with the adhesive, in order to increase strength or decrease oxygen
  permeability;
- other soils, such as silt, as the lowest-cost fillers available;
- encapsulated air bubbles to reduce density, such as hollow
  microspheres of steel, glass, or plastic;
- vermiculite, perlite, pumice, or similar foamed minerals to reduce
  density;
- polystyrene foam beads or similar foamed plastic beads to reduce
  density;
- lead, bismuth, or steel particles to increase density;
- rubber particles to increase shock damping and reduce rigidity;
- graphite, amorphous carbon, or silicon carbide to increase
  electrical conductivity and/or heat resistance;
- donors of alkali metals and boron to reduce melting point and
  increase the thermal coefficient of expansion, for example to
  facilitate fire-glazing the surface afterwards — carbonates of
  sodium and potassium, boric acid, and borax are traditional here;
- foaming agents like baking powder;
- plasticizers like phthalate esters;
- chopped fibers, for example of basalt fiber or any of the other
  types mentioned earlier, or other fibers such as hair clippings,
  sawdust or other wood fibers, grass clippings, used yerba mate,
  bamboo fibers, or straw;
- broken glass, for example for decorative purposes;
- abrasives, such as aluminum oxide or silicon carbide;
- pesticides such as copper chloride or blue vitriol to prevent
  biodegradation, for example by insects eating wheat paste and cotton
  fibers;
- UV blockers such as titanium dioxide to prevent photodegradation;
- milled mica, stainless steel, aluminum powder, or other glitters for
  a sparkly metallic appearance;
- catalysts;
- sodium polyacrylate or similar hygroscopic polymers to make the
  surface hygroscopic or cause changes in shape with environmental
  humidity;
- other fillers and additives;
- some mix of the above.

In some cases you will want to start with a lightweight,
fast-hardening system such as gauze and plaster of Paris, then overlay
it with a heavier system that the balloons alone wouldn't be able to
support.  There are many other reasons you might want to use multiple
layers, including making sandwich panels with a light, weak inner core
and stronger faces, and allowing earlier layers time to dry.

If you combine this process with the foam-filling process you can get
shapes built from tubes with strong, rigid, hard surfaces braced by a
weaker foam within.

Also, of course, most of these processes can be used on top of a form
produced by some other method than globoflexia; for example, 3-D
printing, bending a wire armature, using an existing object such as a
vase, blowing glass, vacuum-forming plastic, blow-molding plastic,
electrotyping, and so on.
