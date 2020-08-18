I heated up the tip of my zirconia knife to orange heat yesterday.  To
my surprise, much of the blade turned black; I guess the knife had
some oil on it, though I thought it was clean.  In the hotter parts of
the blade, this carbon deposit burned away, but in the cooler parts it
remained.  Steel wool and brass wool proved ineffective at removing
the deposits.

This led me, as most things do, to thinking about 3-D printing.
Suppose instead of depositing a hot liquid onto a cool workpiece which
then freezes the liquid in place, as FDM printers do, we deposit a
*cool* liquid onto a *hot* workpiece, which pyrolyzes it into a solid?
Sort of like chemical vapor deposition, but from a liquid so you can
deposit it selectively?

Charring organics into carbon
-----------------------------

So, for example, you could deposit warm bitumen and pyrolyze it to
carbon at around 350°.

Almost any organic substance would work; so, for example, vegetable
oil, sugar, starch, and dissolved gelatine would all work, but
possibly other things would work better.  Small molecules tend to
volatilize before carbonizing (though any cook can tell you that even
light vegetable oils will carbonize before volatilizing completely,
though perhaps not before migrating to cooler parts of a surface where
you don't want them), molecules with a lot of oxygen tend to produce
more bubbles, and highly saturated molecules (like the ones in
bitumen) and aromatic molecules are more resistant to pyrolysis, so
perhaps the ideal substance would be a high-molecular weight, highly
unsaturated aliphatic hydrocarbon.  (However, aromatic groups tend to
promote cross-linking, which helps to prevent volatilization and
melting before pyrolysis.)

Moreover, it would at least remain viscous at pyrolysis temperatures,
like polycaprolactone (not to be confused with polycaprolact_am_,
which is common nylon 6 and not viscous at all), though
polycaprolactone itself is saturated and contains oxygen.  Something
that solidifies before reaching pyrolysis would be even better (see
below about polymer-derived silicon-based ceramics.)  Somewhat
polymerized linseed oil is a good possibility.  Nylon 6,6 and nylon 6
are not very viscous or unsaturated but are a good possibility; their
amide bonds could play the role of the unstable unsaturated bonds.
The urethane groups of polyurethanes contain both double bonds and
amide bonds, making them especially promising, though the popular
polyurethanes are highly aromatic.  Polyisoprene, especially if
vulcanized into a thermoset with sulfur, would work perfectly.

Decomposition can be accelerated with additives; PET notoriously takes
up water from humid air and then hydrolyzes rapidly at melt
temperatures if not dried, so ordinary water may be a viable option,
despite its tendency to produce large bubbles.  Acids and bases may
also help to accelerate such decomposition — ideally for this process
the additive would itself volatilize or decompose, leaving only
carbon.  Ammonia, sulfuric acid, nitric acid, acetic acid, formic
acid, and hydrogen cyanide are possible degradation-enhancing
additives.  Cellulose acetate has a well-known autocatalytic
degradation reaction with acetic acid, but this produces a goo which
may be too liquid at pyrolysis temperatures.

Additives like alkali metals and halogens might accelerate
decomposition as well, but would probably remain in the final product.

Using thermosets such as the aforementioned vulcanizing polyisoprene
has the advantage that you don't have to worry about the feedstock
melting and running off the workpiece before charring, so you don't
need to prefer unsaturated aliphatic compounds.  Normally thermoset
polymerization is tightly controlled to reduce the risk of heat
degradation of the material produced, but in this context that ceases
to be a problem.  So any of the common thermosets — phenolics,
epoxies, polyisocyanurates, urea resins, thermosetting polyesters like
Lucite, melamine resin — should be fine.  Thermosetting is the
approach universally taken for preceramic polymers used for producing
silicon-based ceramics.

Charring polymers into silicon carbide, silicon nitride, and silicon oxynitride
-------------------------------------------------------------------------------

[People have already done this since the 1970s][0], though, until
recently, mostly to produce fibers such as Nicalon and Tyranno, and
coatings.  The technique is called "preceramic polymers", "precursor
ceramics", or "polymer-derived ceramics", though typically in that
technique the polymer shape is fully formed before pyrolysis
begins — an approach that can be taken for all of the materials
discussed in this note, including carbon and the metal oxides
discussed below.

[0]: http://www.ing.unitn.it/~soraru/download/149-FeatureJACS.pdf "Polymer-derived ceramics: 40 years..., Colombo et al., 2010"

Large-molecule silicones are usually thermosets.  Polydimethylsiloxane
((SiO(CH₂)₂)ₙ) has a 2-to-1 carbon-to-silicon ratio, which is twice
the ideal for producing silicon carbide, so polymers that have been
used instead include carbosilazane resin, poly(methylsilazane),
poly(methylchlorosilane), and poly(carbosilane), which pyrolyze in
nitrogen to silicon carbide, yielding a ceramic whose mass is some
60–75% of that of the original polymer (the "ceramic residue yield").

An excess of silicon is preferable to an excess of carbon for
producing high-temperature ceramics, since silicon melts at "only"
1414°, while carborundum is stable to 2830° and graphite is stable to
3642°.  Poly(carbosilane), (H₂SiCH₂)ₙ, pyrolyzes in nitrogen to
essentially pure carborundum, but in other precursors some carbon is
lost as methane or carbon monoxide during pyrolysis.

To get silicon nitride instead, an ammonia atmosphere is required both
to supply more nitrogen than can be jammed into the polymer and to
cleave off unwanted methyl groups.  It is helpful but not necessary
for the original polymer to contain nitrogen; in fact, ammonia
pyrolysis can convert Nicalon to silicon nitride.

An attractive aspect of these processes is widely reported to be that
low temperatures, in the 1100°–1300° range, are sufficient to produce
these ceramics by pyrolysis, while the standard sintering processes
require 1800° or more, and additionally contaminates the ceramic
produced with "sintering aids", in order to avoid even higher
temperatures.  So not only can polymer-derived ceramics withstand
higher temperatures than are required for their production, they can
even withstand higher temperatures than the same ceramics when they're
processed conventionally!

Some of these processes require a "curing" step in between plastic
forming, such as spinning, and the pyrolysis step, in order to keep
the plastic from melting before pyrolysis is complete.  This curing
may happen by way of cross-linking, as in rubber vulcanization, or by
evaporation of solvents and other plasticizers.  This approach is also
applicable to pyrolytic carbon production described above.

A problem that commonly afflicts these processes is structural damage
due to pyrolytic gas production during pyrolysis, which is a major
reason for requiring high ceramic residue yields.  As with traditional
fired-clay ceramics, this is a bigger problem with thicker material
sections (nonexistent below a few hundred microns), and it is to be
expected that an incremental additive process in which the material is
pyrolyzed before more material is laid on top of it should enable the
fabrication of thicker cross-sections.

Another problem that commonly afflicts these processes is dimensional
inaccuracy due to shrinkage during pyrolysis, and deposition during
pyrolysis will also reduce this problem, since the shrinkage will
affect individual beads as they are laid down, not the fabricated
article as a whole, whose dimensional precision will be determined
grossly by the positioning precision of the end-effector and only
finely by shrinkage and wiggle.

Of course, there are certain practical difficulties attending the
construction of a "hotend" and manipulator that can function in an
environment that keeps the workpiece at 1100°–1300°.  A combination of
liquid-cooling and refractory insulation for a manipulator arm would
probably be necessary.  The thermal gradient near the deposition point
poses additional difficulties: the cooler material being deposited
onto the hot workpiece will locally cool and contract the workpiece,
inducing stresses that could produce cracks.

Boron nitride, aluminum nitride, boron carbonitride, silicon
oxycarbide, silicon carbonitride, SiCNO, SiBCN, SiBCO, SiAlCN, and
SiAlCO have also been synthesized by this route.  Some of these
ceramics cannot been synthesized in any other known way.

Exposure to reactive substances has been used instead of or in
addition to heating to remove the unwanted moieties.  Examples include
ammonia, nitrogen dioxide, reactive plasma, and highly alkaline
solutions.  These approaches could likely also be used with the other
materials discussed in this note; incremental deposition of the
feedstock, as by fused deposition modeling, would give the reactive
environment access even to material that is ultimately buried inside
the part.

Of special note here is HRL Laboratories' high-density
stereolithography resin, which produces almost-fully-dense silicon
oxycarbide when pyrolyzed at 1000° in argon ("Additive manufacturing
of polymer-derived ceramics", Science, January 2016, many authors and
Tobias Schaedler).  Their recipe is mercaptopropyl methylsiloxane and
vinylmethysiloxane (in a 1:1 molar ratio of thiol to vinyl groups),
plus the usual cocktail of stereolithography additives; pyrolysis
resulted in "42% mass loss and 30% linear shrinkage" to amorphous
SiO₁.₃₄C₁.₂₅S₀.₁₅ but apparently no porosity or surface cracks.  To
reduce porosity and cracking, they limited feature size to 3 mm and
heating to 20°/minute (or, according to their supplemental materials,
1°/minute), but it is not clear to me what the crucial factors were.

Metal and semimetal oxides
--------------------------

(For the purpose of the following, consider "metals" to include boron,
silicon, and aluminum as well as the usual metals.)

A number of metal oxides form minerals with desirable properties, and
it might be desirable to form them into particular shapes; many of
these metal oxides are themselves refractory and chemically resistant,
so casting or dissolving them is difficult.  In particular, the oxides
of aluminum, zirconium, silicon, titanium, chromium, thorium, and
uranium are all hard, refractory ceramics, most occurring naturally as
minerals.

But perhaps salts of the same metals can be formed into the right
shape, whether as an solution (for example in water), a gel, a paste,
or as solid particles; then calcined to yield the oxides?  As the
preface to the IUPAC–NIST Solubility Data Series volume on formates
said in 2001:

> Bivalent metal formates could be used as precursors for the
> production of catalysts because they show excellent miscibility in
> the solid state, i.e., they form mixed crystals that dissociate at
> relatively low temperatures (about 300 °C) to form the respective
> oxides and mixed oxides.  Catalysts for the decomposition of
> alcohols have been prepared by the thermal decomposition of Ni and
> Mg formate mixed crystals, from Cu and Mg formate mixed crystals,
> and from the double salts CuSr₂(CHO₂)·8H₂O and
> CuBa₂(CHO₂)₆·4H₂O. ...

For this we need metal salts that decompose on heating, but ideally
are [soluble] in water ([IUPAC-NIST database]); moreover, they
probably need to be soluble *together* so they don't precipitate in
the nozzle.  Basically these are either metal cations with anions that
decompose on heating — nitrate, sulfate, or organic anions — or
ammonium or hydronium with metal-complex anions.  Tetramethylammonium
is a possible alternative cation but for now I'm going to ignore it.
Here's a list of candidates.

[soluble]: https://en.wikipedia.org/wiki/Solubility_table
[IUPAC-NIST database]: https://srdata.nist.gov/solubility/index.aspx

| cation           | anion         |      g/100g | decomposition  |
|                  |               |       water | temperature    |
|                  |               |     (20° if |                |
|                  |               |   possible) |                |
|------------------+---------------+-------------+----------------|
| aluminum         | nitrate       |          74 | 150°           |
|                  | sulfate       |          36 | 900°           |
|                  | formate       |           6 |                |
| chromium(III)    | nitrate       |          81 | 100°           |
|                  | sulfate       |   "readily" | 700° (to acid) |
| ammonium         | dichromate    |        high |                |
|                  | paratungstate |       high? | 600°           |
| (hydronium)      | boric acid    |         low |                |
|                  | chromic acid  |         169 |                |
|                  | alumic acid   |             |                |
|                  | tungstic acid |         low |                |
|                  | titanic acid  |             |                |
|                  | zirconic acid |             |                |
| calcium          | nitrate       |             | 500°           |
|                  | acetate       |             | 160°           |
|                  | formate       |             | 200°?          |
|                  | sulfate       |        ≈0 ☹ |                |
| magnesium        | acetate       |          53 |                |
|                  | oxalate       |         low | 620°           |
|                  | chromate      |         137 |                |
|                  | formate       |          14 |                |
|                  | nitrate       |        69.5 |                |
|                  | sulfate       |          35 |                |
| zirconium        | sulfate       |        52.5 |                |
|                  | nitrate       |         yes | 100°           |
|                  | acetate       |           ? |                |
|                  | formate       |           ? |                |
|                  | tungstate     |         low |                |
| titanium         | sulfate       |           ? |                |
|                  | nitrate       |           ? |                |
|                  | formate       |           ? |                |
|                  | acetate       |           ? |                |
| cobalt           | nitrate       |          84 |                |
|                  | sulfate       |        less |                |
| copper(II)       | nitrate       |        83.5 |                |
|                  | sulfate       |        less |                |
|                  | formate       |           7 |                |
| ferrous ammonium | sulfate       |          27 |                |
| iron(II)         | nitrate       |         134 |                |
|                  | sulfate       |          29 | 680°           |
|                  | oxalate       |        poor |                |
| iron(III)        | nitrate       |         138 |                |
|                  | sulfate       |      slight |                |
|                  | oxalate       |      slight |                |
|                  | chromate      |  decomposes |                |
| lead(II)         | acetate       |          44 |                |
|                  | nitrate       |          54 |                |
|                  | sulfate       |        ≈0 ☹ |                |
| lead(IV)         | acetate       | "reversible |                |
|                  |               | hydrolysis" |                |
| nickel           | acetate       |       high? |                |
|                  | nitrate       |          94 |                |
|                  | sulfate       |          44 |                |
|                  | formate       |        low? |                |
| tin(II)          | sulfate       |          19 |                |
|                  | nitrate       |           ? |                |
| yttrium(III)     | acetate       |           9 |                |
|                  | formate       | 26 (at 50°) |                |
|                  | nitrate       |         123 |                |
|                  | sulfate       |           7 |                |
| zinc             | formate       |         5.2 |                |
|                  | nitrate       |          98 |                |
|                  | sulfate       |          54 |                |
|                  | acetate       |          30 |                |
| thorium(IV)      | nitrate       |         191 |                |
|                  | sulfate       |        ≈0 ☹ |                |
| uranyl           | nitrate       |         122 |                |
|                  | sulfate       |          21 |                |
|                  | acetate       |           8 |                |

I can't find any concrete information about ammonium aluminate; I
suspect it doesn't exist, although a number of chemical suppliers have
it in their catalogs.  Ammonium silicate apparently does exist but is
too finicky to be of any practical use.  Ammonium borate also seems to
exist, but information about it is rare.

Tetraethyl orthosilicate is commonly used in a way similar to this to
produce silica gel, but it is itself liquid rather than being
water-soluble, and its decomposition is driven by exposure to water,
not to heat.

Halogen complexes might be another thing to check out: titanium and
zirconium both complex with halogens, and it may be possible to drive
off the halogens with enough heat.

Glasses (frits) of metal oxides melt at lower temperatures; may be
suitable fillers

Titanium, zirconium, aluminum, magnesium, chromium

Aluminum: nitrate (74%, decomposes at 150°) and sulfate (36%,
decomposes below 900° to SO₃ and cubic γ-alumina) are highly soluble.
Also occurs in soluble aluminates, but there is no aluminate of
ammonia, so you can't get alumina by calcining it; strontium aluminate
is a glow-in-the-dark pigment and a refractory cement good to 2000°.

Chromium: ammonium dichromate is fairly soluble.  Chromium(III)
nitrate and especially sulfate are highly soluble; hexavalent chromium
oxide too, but we don't want that.

Boric acid is fairly soluble in water at 100°, nearly half as much at
50° (13% or so)

Calcium: nitrate is highly soluble, decomposes at 500°; soluble
acetate releases acetone at 160° leaving carbonate; soluble formate
decomposes at 300°, maybe to CaOH and CO, or like NaCOOH to an oxalate
(CaC₂O₄, insoluble) and hydrogen (at 360° for Na?), then to a
carbonate releasing carbon monoxide (at 290° for Na, 200° for calcium
oxalate)?  Calcium will precipitate lots of things including sulfate.

Magnesium acetate 53%; chromate 137%; formate 14%; nitrate 69.5%;
sulfate 35%.

Zirconium: sulfate 52.5%.  Nitrate has been successfully calcined to
produce zirconia: <https://pubs.acs.org/doi/10.1021/cm060883e>

Titanium: 

cobalt? vanadium? manganese? nickel? copper? zinc? tin? bismuth?
strontium? barium? lithium?

Cobalt nitrate is 84% soluble in water; sulfate a bit less so.

Copper(II) nitrate is 83.5% soluble in water, substantially more than
sulfate.

Ferrous ammonium sulfate is 27% soluble.  Iron(II) nitrate 134%;
sulfate 28%; iron(III) nitrate 138%.

Lead acetate 44%; lead(II) nitrate 54%.  Lead(II) will precipitate
sulfate.

Lithium acetate 40.8%; nitrate 70%; sulfate 34.8%; tartrate 27%.

Nickel acetate "easily soluble", nitrate 94%; sulfate 44%; everything
else pyrolyzable almost insoluble.  (Its highly soluble chloride is
not relevant.)

Ammonium paratungstate pyrolyzes to tungsten trioxide at 600°, which
is the soft mineral tungstite; the paratungstate ion has a tendency to
precipitate from aqueous solution over time.  There's also a
"metatungstate" oxyanion with 12 tungstens in it which is more soluble
and stable in highly acidic solution.

Tin sulfate 19%; nitrate?

Yttrium: Yttrium(III) acetate 9%, nitrate 123%, sulfate 7%.

Zinc: formate 5.2%, nitrate 98%, sulfate 54%, acetate 30%.

Uranium, thorium

Thorium: thorium(IV) nitrate 191%, sulfate almost insoluble.

Uranium: Uranyl nitrate 122%, sulfate 21%, acetate 8%.

Filled systems
--------------

A common use for preceramic polymers, apart from the fibers and
coatings mentioned earlier, is as polymeric binders for powdered
ceramic — perhaps the same ceramic that the polymer will pyrolyze to.
Filled systems like this have a variety of advantages:

- The resulting part, if the filler consists of fully-dense particles
  of the same ceramic, is denser and therefore stronger than if made
  entirely from the polymer.
- The powder may be easier to produce than the polymer, reducing cost,
  or even naturally abundant, as with quartz.
- Controlled composites of different materials, or materials of
  different morphologies, can be thus produced; this may improve
  mechanical properties or simply be cheaper.
- Other filler powders can be used to provide other properties; for
  example, early-90s research at MIT (Seyferth et al., 1992) )produced
  silicon-carbide/metal-carbide composites from poly(methylsilane) and
  organometallic polymers, but found it necessary to mix in metal
  powder to eliminate free carbon from the pyrolysis result.
- The grain structure of the resulting material can be controlled more
  precisely and customizably than that of objects made by liquid
  casting or sintering.
- The filler may be adequate to maintain the shape of the material as
  it heats up to the pyrolysis temperature, even if the liquid does
  not cross-link to form a thermoset.
- The filler may provide egress paths so the gases evolved during
  pyrolysis don't crack the nascent ceramic structure, even if the
  filler itself burns out before pyrolysis of the ceramic precursors,
  thus forming a porous green structure.  This is the "Ceramicore"
  process by Weifeng Fei; it's also a traditional way of making
  insulating refractory bricks from fired clay and organic fillers
  like sawdust, but Fei's process infuses a liquid preceramic polymer
  into a continuous fibrous matrix.

The point about controlled composites bears further exploration.  For
example, pure amorphous carbon is quite weak, but if used in small
quantities to cement iron filings, the composite would achieve
significant strengths.  Like cancellous bone, a porous composite made
by pyrolyzing a binder between fully-dense whiskers of a ceramic will
tend to be far more fracture-resistant than the same material if
nonporous.  A mixture of different kinds of particles can provide
desirable combinations of properties unachievable in a homogeneous
material, such as high surface hardness along with high crack
resistance — again, like bone.  Anisotropic filler orientation can
provide anisotropic mechanical properties — again, like bone, or wood.

Ferromagnetic fillers like powdered iron can make a ferromagnetic bulk
material with low electrical conductivity, but ceramic binders can
maintain dimensions at different temperatures more precisely than the
usual organic binders used for powdered-iron cores; also, iron's Curie
temperature is 770°, which many ceramics can withstand easily, but
organic binders can't even approach.  (And cobalt's is 1115°!)

The cheapest possible combinations would be sugar or flour with quartz
sand or glass fiber, but at least in my low-temperature,
poorly-controlled experiments (up to perhaps 400°–600°) the carbon
resulting from sugar pyrolysis adheres very poorly to glass,
represented by the glazing of stoneware, and to quartz sand.  I could
scrub it off easily with steel wool, and even scratch off some with a
fingernail.  Surface preparation, for example with plasma (perhaps in
a fluidized bed), could conceivably improve the situation.  Carbon
should stick well to carbon fiber, though, and many things stick well
to glass.  And, as I said above, to my sorrow carbon sticks
beautifully to zirconia.

Magnesium oxychloride
---------------------

Boron nitride
-------------

in ammonia?

Olivines
--------

Forsterite, including peridot, is Mg₂SiO₄, while fayalite is Fe₂SiO₄;
these are the endmembers of the olivine spectrum.  Calcium cation
substitutions also occur, modifying the structure and making it
softer, going all the way to larnite, the belite of portland cement.

Mullite
-------

Ordinary clay pottery
---------------------

Self-propagating high-temperature synthesis
-------------------------------------------

Other
-----

Titanium carbide?  Zirconium carbide (3530°)?  Tantalum carbide
(3850+°)?  Zirconium diboride?  Gallium nitrate (soluble, decomposes
to GaN in flowing ammonia at 500°–1050°)?
