Globoflexia, or balloon twisting, is a popular form of entertainment,
especially for children; a skilled balloon twister can make an
evocative, if cartoonish, sculpture of an animal or person within a
few seconds to a minute.  Extremely elaborate sculptures are feasible
over a few hours; because the material is so light, getting all of its
compressive strength from air, sculptors can easily build and
manipulate sculptures far larger than themselves.  Because the
balloons leak, the sculptures are ephemeral, lasting at most a few
days.

Much to my surprise, there’s a world globoflexia conference every two
years at which teams from dozens of countries compete.

What if you could use globoflexia as a medium of expression for more
permanent ideas?  Obviously you can photograph the sculptures, thus
making images or videos of them, but the underlying three-dimensional
form of the object is lost.

So I’ve been thinking about three different ways to do this:
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

Moreover the balloon sculptures’ shapes are themselves well-behaved:
the surface at most points has a relatively smooth curvature
determined mostly by the gauge pressure and the tensions in two
directions.  Rubber’s complex pseudoelastic thermodynamic behavior is
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
which accommodates itself to the container it’s in before slowly
polymerizing into a light, hard, thermally and electrically insulating
foam with substantial mechanical strength.  Some formulations form
waterproof closed-cell foams, while others form lighter-weight
open-cell foams.  There are formulations that ship as pairs of liquids
to be mixed in a gun, for high-volume applications, and other
formulations that you just squirt out of a can.

The materials that form polyurethane foams are fairly reactive until
they’ve finished forming the foam, and that may be a fatal flaw for
squirting them into rubber balloons: they may corrode the balloons and
pop them before the foam has hardened.

Polyurethane is not the only possible hardening foam.  Latex foam is
widely used for pillows, mattresses, and theater special-effects
makeup (“prosthetics”), in which last use it is typically cast in
molds before curing.  Protein foam is a popular dessert, both as
meringue (with air whipped into it) and as gelatin foam in so-called
“molecular gastronomy” or “modernist cuisine”, where the gelatin gel
is mixed with nitrous oxide under high pressure and low temperature,
like canned whipped cream.  Gelatin foam is also widely used for
makeup, sometimes whipped like meringue, but sometimes foamed with
non-double-acting baking powder (for example baking soda with cream of
tartar) or even yeast.

There’s also been a lot of work in recent years on foamed concrete,
sometimes called “aircrete”.  This consists of portland cement, water,
a surfactant (Suave shampoo is reputed to work well, though there are
also specific surfactant mixes from companies like Drexel), possibly
some foam stabilizers (I suspect gelatin might work well for this),
and a great deal of gas.  Sometimes sand is used, but rocks are never
used.  The original 1920s process for foaming concrete (“autoclaved
aerated concrete”) used aluminum powder mixed into the concrete mix.
After molding the concrete was heated in an autoclave to react the
aluminum with some of the lime in the concrete, thus foaming the
concrete with hydrogen gas, as well as accelerating the formation of
the calcium silicate hydrates that bond the concrete.  The more common
method nowadays is more like meringue: air is mechanically mixed into
some of the water before mixing in the wetted cement.

You probably can’t foam any traditional concrete with anything similar
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
Radio-controlled airplane hobbyists commonly mix a secret “foaming
agent” from R&G into two-component epoxy to get an epoxy foam, for
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
- hide glue;
- silicone;
- epoxy resin and similar resin systems, if they don’t attack the
  balloons;
- cyanoacrylate adhesive;
- plaster of Paris (thank you, Javier Candeira!);
- sodium silicate waterglass;
- slaked lime, if it doesn’t attack the balloons or fibers;
- portland cement, if it doesn’t attack the balloons or fibers and the
  color isn’t a problem;
- calcium aluminate cement, if it doesn’t attack the balloons or
  fibers, and refractoriness is desired;
- Sorel cement, if it doesn’t attack the balloons or fibers, and
  maximal strength is desired;
- a so-called “geopolymer cement”;
- latex paint;
- shellac;
- polyurethanes;
- urea-formaldehyde resin;
- phenolic resin;
- various kinds of solvent-based plastic cements such as PVC dissolved
  in acetone, if the solvent doesn’t attack the balloons or fibers;
- constant-tack adhesives like those used in scotch tape;
- wet clay, whether simply allowed to dry or later fired;
- tar, though probably using a solvent rather than heat;
- paraffin or other waxes, if the balloons can handle their melting
  temperatures;
- spray foam;
- linseed oil;
- castable refractory mix;
- other adhesives;
- some mix of the above.

For the fiber reinforcement, instead of paper, you could use:

- nothing;
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
- large flakes of mica;
- ceramic fiber like those used in refractory blankets and flocking,
  if resistance to high temperatures is desired (typically these
  fibers are mixes of mullite, alumina, zirconia, and silica);
- basalt fiber;
- webbing like that used in car seatbelts, made from nylon or other
  fibers;
- steel window screens;
- aluminum or fiberglass window screens, if the binder is not strongly
  basic;
- stainless steel cloth;
- thicker and stronger metal reinforcement such as traditional rebar
  tie-ups, hardware cloth, chicken wire, or expanded sheet metal;
- copper wires;
- gel-spun ultra-high-molecular-weight polyethylene fibers;
- nonwoven bargain-basement felted polyester fabric (“friselina”);
- other fibers;
- some mix of the above.

If the adhesive is less flexible than the fiber reinforcement (e.g.,
has a higher Young’s modulus), then the fiber reinforcement may just
weaken the binder instead of strengthening it, although it can produce
some “strain hardening” behavior where the adhesive cracks but the
fiber keeps the adhesive cracks from opening wider and thus continuing
to propagate.  Still, even weak fibers can hold the adhesive in
position until it sets, and for some purposes the adhesive alone will
be strong enough without any help from the “reinforcement”.

The fiber reinforcement may have other purposes as well, other than
shaping or strengthening; for example, if the adhesive is transparent,
decorative or informative images can be printed on the fiber
reinforcement; conductive fiber reinforcement can provide Faraday-cage
protection against EMI more cheaply and flexibly than sheet metal;
gold leaf or aluminum foil can provide high reflectivity; and so on.

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
  horsehair, paper fibers as in ordinary papier-mâché,
  sawdust or other wood fibers, grass clippings, used yerba mate,
  bamboo fibers, or straw;
- broken glass, for example for decorative purposes;
- abrasives, such as aluminum oxide or silicon carbide;
- pesticides such as copper chloride, blue vitriol,
  salt, or clove oil to prevent
  biodegradation, for example by insects eating wheat paste and cotton
  fibers;
- UV blockers such as titanium dioxide to prevent photodegradation;
- additives to increase effective heat capacity, such as
  microencapsulated phase-change materials;
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
it with a heavier system that the balloons alone wouldn’t be able to
support.  There are many other reasons you might want to use multiple
layers, including making sandwich panels with a light, weak inner core
and stronger faces, and allowing earlier layers time to dry.

In cases where the first layer has no fiber reinforcement, it might be
useful for that first layer to be sprayed onto the balloons rather
than placed there by hand.  This would allow it to be applied more
rapidly and easily, and it could perhaps be strong enough once
hardened to support significantly more weight than the balloons
themselves.  Spray foam seems particularly appealing for this
application.

If you combine this process with the foam-filling process you can get
shapes built from tubes with strong, rigid, hard surfaces braced by a
weaker foam within.

Also, of course, most of these processes can be used on top of a form
produced by some other method than globoflexia; for example:

- 3-D printing;
- bending a wire armature;
- using an existing object such as a vase;
- blowing glass;
- vacuum-forming plastic;
- blow-molding plastic;
- electrotyping;
- modeling with clay or other modeling compounds;
- origami, whether with paper, sheets of PET or other plastic,
  aluminum foil, or other materials;
- commanding a motorized reusable “armature” to assume a certain
  position until the papier-mâché draped over it hardens;
- CNC machining;
- cutting, folding, and assembling shapes out of cardboard or MDF,
  though this requires special attention to the adhesive’s water
  content;
- laser cutting;
- piling up sand or other soil, whether with an additional binder or
  not;
- assembling Legos, Meccano, Ramagon, Heckballs, modular T-slot
  aluminum framing, or other reusable “construction set” parts;
- building latticework structures out of other kinds of members, for
  example, metal trusses or Tensegrities like Kenneth Snelson’s;
- manual carving of carveable materials such as metals, wood, foamed
  concrete, alabaster, graphite, lightweight refractory bricks, tuff,
  sandstone (natural or artificial), or papercrete;
- hot-wire cutting of fusible foams such as styrofoam or
  polyisocyanurate;
- inflatable shapes made in ways other than balloon twisting; for
  example, connecting sheets of polyethylene into a large balloon
  sculpture using a hot-wire heat-sealing machine;
- assembly of a variety of objects, for example with hot glue;
- forming sheet metal, for example by hammering, stamping,
  single-point incremental forming, or bead rolling;
- assembling panels or other shapes cut or otherwise shaped from
  closed-cell polymer foam or other materials;
- carpentry;
- basket weaving, whether from traditional materials such as bamboo
  and rattan or non-traditional materials such as Ethernet cable and
  sheet-metal strips;
- other techniques for producing three-dimensional shapes;
- some combination of the above.

In some cases it will be most convenient to apply the adhesive to the
fiber reinforcement after it is already in position, but in other
cases, especially with porous adhesives, it will be most convenient to
combine them ahead of time.

During the process of adding layers of fiber reinforcement and
(possibly filled) adhesive, it may be convenient to embed other
elements in the object being constructed, in non-random positions.
For example, you can embed sensors, heating elements, LEDs or other
lights, antennas, pancake coils, and wires to feed all of these.  For
some purposes it is best to cover these with a layer of adhesive
and/or fiber, for example to prevent abrasion or electrical short
circuits, while for other purposes exposed electrodes or other
actuators may be useful.

### Specific combinations ###

The above outlines a large design space of processes, a few of which
are already in use:

- The ordinary kinds of papier-mâché, which commonly use untwisted
  balloon forms, but sometimes wire armatures.
- “Cloth mache” [sic] in which fine cotton cloth is used instead of
  paper, either as the last layer or as several layers, sometimes with
  PVA glue rather than wheat paste.
- Duct tape, masking tape, strapping tape, scotch tape, and electrical
  tape are all composites of adhesive with reinforcing fibers;
  strapping tape fibers are often parallel glass fibers with a PET or
  polypropylene backing, masking tape is paper, scotch tape is
  cellophane, electrical tape is generally plasticized PVC, and duct
  tape is often polyester scrim with an LDPE backing.  (Velma Stoudt’s
  original “duck tape” used cotton duck, as the name implies.)  Duct
  tape also typically includes a powdered aluminum filler in the LDPE
  for reflectivity.  All of them can use a wide variety of so-called
  pressure-sensitive adhesives.  So, pre-combining the adhesive with
  the reinforcing fiber makes for a very convenient and versatile way
  to make and repair things.
- Découpage, in which the “fiber reinforcement” is primarily
  decorative and the adhesive is transparent, and the form is usually
  made by carpentry rather than globoflexia.
- Standard fiberglass composite construction uses one or more layers
  of fiberglass cloth as fiber reinforcement, sometimes laid up on top
  of forms made by hot-wire cutting of styrofoam, then smoothed down
  with epoxy.  Often additional layers of epoxy without cloth are
  added after the first in order to provide a smooth surface without
  any fiberglass sticking out of it.
- “Textile-reinforced concrete” is ordinary portland cement reinforced
  with high-modulus cloth rather than rebar; typical fibers used for
  the textile include carbon fiber, AR glass, and basalt fiber.  The
  forms are typically made of wood or styrofoam rather than by
  globoflexia.  Making TRC forms by globoflexia would likely require
  plastering the balloons first with a lightweight support material
  such as ordinary papier-mâché.
- Concrete canvas inflatable tents use a form made of a large
  inflatable polyethylene bag to support a pre-sewn canvas fiber
  reinforcement pre-impregnated with an adhesive system made of
  portland cement and quartz construction sand, which is wetted with
  water before inflation.
- The same kind of concrete canvas is commonly used as a geotextile,
  in which case the form is just the earth’s surface.
- “Ferrocement” uses a form made by bending rebar and “fiber
  reinforcement” made of lighter-weight metal cloth, such as hardware
  cloth or chicken wire; once the first layer of fiber reinforcement
  is in place on the form, an adhesive system of typically portland
  cement, quartz construction sand, and water is troweled on, often
  followed by more layers of fiber reinforcement.  Usually a layer of
  adhesive is also added to the *inside* of the structure to cover up
  the steel.  The first layer of adhesive often includes a
  chopped-fiber additive more to control the rheology of the mix so it
  doesn’t fall through the holes in the fiber reinforcement than to
  strengthen the final product.  This has been used for decades for
  lightweight buildings and cheap boats.
- Very similar to ferrocement, fine-art sculptors commonly build forms
  as bent-wire armatures, cover them with chicken-wire fiber
  reinforcement, and trowel on a plaster of paris adhesive, with or
  without strengthening fillers of sand and, for example, horsehair or
  sisal.
- Traditional lath-and-plaster construction makes forms of lath rather
  than balloons, mixes horsehair and sand into the plaster-of-paris
  adhesive additives to improve strength, and uses no further fiber
  reinforcement.
- Traditional gauze-and-plaster medical casts for healing broken bones
  were an inspiration, as mentioned above.  In this case the form is
  grown in a womb rather than twisted from balloons.  This technique
  turns out to go back to the Middle Kingdom of ancient Egypt, around
  4000 years ago; archaeologists call it “cartonnage”.
- Shotcrete has been on many occasions sprayed onto inflated domes to
  make Barbapapa-style houses.  In this case the balloons are very
  large and not twisted.  Typically the concrete is portland cement,
  sand, water, and chopped fiber, which last helps reduce slumping.
- Papercrete is a very-low-compressive-strength, low-weight portland
  cement concrete where consisting of portland cement, water, and
  cellulose fibers from paper.  Sometimes it is applied to forms but
  more commonly it is used to build walls.
- Paperclay is a composite of cellulose fibers from paper, clay, and
  typically sand or grog.  It is usually shaped by hand rather than
  being applied to forms.  It has superior green strength to more
  common clay materials, reputedly improving freedom of modeling.
  When fired, the paper burns out, leaving a lightweight porous
  material.
- I have seen one person explain their system for constructing
  lightweight RV furniture from blocks of styrofoam cut with a hot
  knife, assembled, and then bonded together by coating the inner and
  outer surfaces of the furniture with window screens glued to the
  foam with latex paint.  This forms sandwich panels whose compressive
  and shear stiffness come from the foam and whose tensile and
  flexural strength comes from the window screens.

However, the systematization suggests many new promising combinations.
For example:

#### Lime-concrete furniture ####

The initial form is produced by globoflexia and wrapped in three
layers of gauze strips dipped in fresh plaster of Paris.  Fifteen
minutes later, steel window screens are draped over the plaster frame,
and a thick mix of slaked lime, water, quartz construction sand, and
chopped fibers is troweled onto the screens.  Two more such layers of
screen and lime plaster are immediately applied.  A few hours later,
the outer surface is painted with sodium silicate to increase its
resistance to abrasion; sufficient air can still enter through the
porous plaster and lime cement to cure the piece over the next 24
hours.  Filling the final piece with foamed portland concrete, made in
the usual way, is optional.

#### Cement water pipes ####

A balloon for twisting is inflated but not twisted.  It is wrapped in
five layers of paper towels dipped in wet portland cement, sand, and
chopped basalt fiber, leaving the balloon’s ends exposed.  The entire
resulting concrete tube is wrapped in stretch plastic wrap to keep it
from drying out.  After 48 hours, the balloon is popped, exposing the
inner surface of the pipe, which is then painted with a solution of
potassium silicate to waterproof it.

#### Bargain-basement roofing ####

A light roof metal truss is built by bending and arc-welding together
rebar.  Jute burlap cloth is dipped into hot tar and laid on top of
the truss, one square meter at a time, overlapping squares in a
shingled pattern.  Three layers should be sufficient for a rainproof
roof sufficiently flexible not to suffer damage from hail.  However,
it is a fire menace, it will get very hot in the sun and may drip, and
you cannot walk on it.  Coating the top and bottom surfaces with
aluminum foil will ameliorate these defects slightly.

#### Cheap, lightweight inert pipes ####

A balloon for twisting is inflated but not twisted.  A4-sized paper is
dipped in low-melting paraffin and wrapped around the balloon in three
layers.  Once the paraffin is cool, the balloon is popped or untied
and removed.  The non-knot end of the balloon can be left open,
forming a closed tube, or closed.  To improve strength, a fourth and
fifth layer of paper dipped in two-component resin rather than
paraffin can be added on the outside.

#### Flexible heat-resistant oven mitts ####

The clay form of a hand is slipcast from a clay slip containing a
mildly [acidic flocculating additive] such as vinegar,
using a porous mold made of
plaster of Paris.  The hollow slipcast clay form is demolded and
tightly wrapped in four layers of loosely woven cotton or linen cloth,
richly smeared with high-temperature acetic-acid-catalyzed silicone (“red RTV”).
Once the silicone has cured, the still-wet clay interior is washed out with
water and a mild base such as baking soda in order to deflocculate the
clay.  The resulting piece, once the acetic acid has escaped, can
withstand temperatures up to some 240°; substituting a cloth with
higher-temperature capabilities should allow it to handle 280°
continuously or brief exposures to 320°.

[acidic flocculating additive]: https://digitalfire.com/article/deflocculants%253A%2Ba%2Bdetailed%2Boverview

(Other reversible flocculants might be epsom salts, which can be
deactivated by barium carbonate, and muriate of lime, which can be
deactivated by soda ash or barium carbonate.  I’ve also seen muriate
of lime recommended as a *de*flocculant, presumably to throw down
vitriol in the form of alabaster.)

It might be necessary to protect the cotton from hydrolysis by the
acetic acid before curing is complete; this can be done either by
replacing acetic-acid silicone with more expensive tin-catalyzed or
platinum-catalyzed varieties, or (with lower certainty) by
impregnating the cotton with a buffer of, for example, baking soda.

This is a case where a balloon form shaped by globoflexia is probably
better than clay, actually, because it’s both easier to shape to the
appropriate smooth blobby shape and easier to remove.  But it’s
important to remove it completely, because the balloon latex will
probably fail at a much lower temperature.

#### Translucent all-natural low-VOC objects ####

By wrapping your twisted balloons in gauze soaked in shellac, you can
get a waterproof, light, flexible material that allows significant
light through, due to the gauze’s light weave.  Alcohol is emitted as
the shellac dries, but this is a fast process; once dry the material
emits almost no VOCs.

#### A coarse filter unplugged by heat ####

If you stamp one or two layers of a loose steel mesh such as a window
screen, impregnated with warm paraffin wax, with a die, then you have
a waterproof and chemically inert plug which, at a predetermined
temperature (one calibratable within 5°) will melt and allow liquid to
flow through freely, while filtering out particles larger than the
mesh.  This could be useful for some kinds of over-temperature safety
valves, for example for resin casting, where, if the resin starts to
overheat, the ideal thing to do might be to dump it quickly out of the
mold into something that dilutes and cools it.  It is possible for
this mesh to have a much larger area than the aperture it covers,
which may be desirable for keeping it from getting clogged by
particulates.

Under some circumstances it might be better to use injection molding
to inject the paraffin around the reinforcing mesh.  This would
provide more consistent paraffin thickness but, I think, less
consistent mesh protection thickness.

#### Ultralight tools for corrosive environments ####

By cutting the shape of a stirrer out of, for example, styrofoam, you
can get a very lightweight tool.  But styrofoam is soluble in all
kinds of solvents, and it’s kind of weak.  By wrapping it with
fiberglass cloth, as is done to construct some boats and aircraft, you
can greatly strengthen it.  A coating of, for example, paraffin,
low-density polyethylene, teflon, epoxy, or polyester casting resin,
could both firmly adhere the fiberglass reinforcement to the foam and
add substantial chemical resistance.

#### Carved aircrete furniture ####

You can pour portland cement foamed in the usual way, by mechanical
aeration of a surfactant-water solution before mixing in the cement,
into forms that are merely blocks.  The next day, once the cement has
partly set, you can sculpt these blocks into desirable shapes using
hand tools like hacksaws, wood rasps, wire saws, hammers, and so on.
The resulting surface will be porous and friable, and therefore not
directly suitable for furniture use, and also an ugly gray unless you
used super fancy portland cement.  Several coats of lime mortar
(slaked lime and quartz construction sand) can give it a hard shell,
perhaps reinforced in key places with copper wires.  The next day, a
coat of polyurethane finish for heavy-duty floors can seal the lime
and provide a softer, warmer surface to sit on or rest your feet on.

#### Fiber-reinforced pottery ####

The usual kind of pottery is fragile.  The ceramic fibers used in
foundry blankets are much less fragile, and some of them can be used
up to 1600°.  You could perhaps take segments of refractory-fiber
cloth like these foundry blankets, dip them in a clay slip, and drape
them over forms (for example, blow-molded from thermoplastic) to make
a shape of two or three millimeters of thickness.  Once the clay slip
was plastic, but before it became leather-hard, you could add another
millimeter of clay to the inside and outside.  After drying and
biscuit-firing these pottery pieces in the usual way, the clay should
be sintered into a solid body; you can get a good biscuit fire out of
at least some ball clays in 6 hours at 1020°, at which temperature
some ceramic fibers are still quite inert.  So they should remain
embedded as fibrous reinforcement in the finished ceramic, making it
dramatically less fragile.

However, care must be taken to ensure that the chemistry of the clay
is compatible with that of the blanket.  Pure zirconia fiber (or
yttria-stabilized zirconia fiber) would probably be perfectly safe,
but I think everybody includes at least alumina and usually silica in
their ceramic foundry blanket fiber.  (Vendors of pure zirconia fiber
say it can be used up to 2200°.)  I suspect that any low-firing clay
would be able to flux and dissolve silica out of part-silica fiber,
and maybe alumina too.  The end product might still be stronger than
ordinary ceramics, though.

Silicon carbide fibers are more widely available than zirconia fibers;
four companies already sell them commercially, at least two since the
1980s (under the names Nicalon, Tyranno, Sylramic, and Ultra SCS).  I
think they are not attacked by clays at common pottery-firing
temperatures, and they are already in use for reinforcing
ceramics — but I think primarily ceramics otherwise made of sintered
silicon carbide, not fired clay.

If desired, a second glaze firing can glaze the pieces to give them
waterproof surfaces and provide protection against abrasion and crack
initiation.  However, this poses the risk that the more aggressive
fluxing of the glaze might attack the fiber reinforcement; this is the
reason for the extra protective layer of clay without fiber in it.  If
this is a problem, a possible alternative to traditional glazing is
waterglass allowed to dry on the ceramic and then crosslinked by, for
example, exposure to calcium chloride.

#### “Ceracement”: refractory “ferrocement” ####

The usual ferrocement recipe uses iron (and consequently a little iron
oxide), portland cement, and quartz, none of which is very friendly to
temperatures above 1000° or 1500°.  Calcium aluminate cement can
replace the portland cement, and olivine, sapphire, or carborundum can
replace the quartz, but what can replace the iron?

Refractory metals like tantalum and niobium are well known, but very
expensive.  Ceramic fibers like those mentioned above (zirconia,
alumina, carborundum) might be adequate; the “ceracement” structure
won’t need flexurally-stiff reinforcement to hold it up, since it can
hold itself up once the cement is set.

At even higher temperatures calcium aluminate fails and needs to be
replaced with higher-temperature castable refractory binders such as
aluminum phosphate.

#### Shatter-resistant grinding stones ####

Modern synthetic grinding stones have a variety of compositions:
sapphire, silicon carbide, cubic boron nitride, etc., bonded with
rubber, thermoset resin, waterglass (mostly historically), Sorel
cement, and so on.  But they tend to fail in a brittle fashion rather
than a ductile fashion, which frequently kills people when they are
spinning fast around people.

Cutoff discs are like thin grinding wheels, but they are usually
reinforced with a fiber, typically fiberglass, I think.

Perhaps grinding wheels could be made with much heavier fiber
reinforcement to encourage them to fail in a more ductile fashion.
High-energy-capacity fibers like rubber, nylon, or music wire might
work better for this than high-modulus fibers like fiberglass and
basalt fiber.

#### Water-activated concrete tape ####

Coat a roll of cotton scrim fabric with a low-temperature nonpolar
thermoplastic adhesive like EVA.  Heat the cloth and run it through a
pile of premixed quick-setting dry lime cement and construction sand,
which sticks to the EVA and coats the cloth.  Allow the cloth to cool
before spooling it onto the takeup roll.  Seal the finished roll
hermetically in a reclosable container.

The resulting tape can be torn by hand like duck tape, although gloves
are advised.  Once a form is wrapped with it to a few millimeters
thick, and flexed into the desired shape, you can moisten the tape
around the form to start the cement setting.  Water can soak through
it easily, and it will amalgamate into a cotton-reinforced mortar
mass.

Perhaps such tape can be laid between bricks or stones to hold them
together, rather than troweling in mortar.

Other cements can be substituted, such as portland cement or calcium
aluminate, which would give stronger results.  There may be
faster-setting high-strength cement formulations that are not in
traditional construction use and that would activate the tape even
faster.  Using plaster of paris instead of the cements suggested would
provide much faster results (and perhaps this is already in use) but
much lower strength.

One particularly interesting possibility is using dissolved
sodium-silicate waterglass as cement, which is somewhat tacky
immediately and will set up hard when dried; but a variety of things
will cause it to set up immediately and become water-insoluble, such
as carbon dioxide gas or, I think, calcium chloride or magnesium
sulfate.  So you could perhaps spray solutions of those on the tape,
once it is applied, from a spray bottle.

Steel wire mesh would be a stronger alternative to cotton scrim, and
might still be possible to tear by hand.
