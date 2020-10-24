Plaster of Paris and lime mortar are widespread, cheap, rigid
materials that can be easily shaped, especially before they harden.
But they don't harden that much.  What if we could harden them
further, once they were shaped, or otherwise strengthen them?

In all of what follows, it’s worth keeping in mind that the material
produced can be formed either as a solid or, sometimes more easily, as
a foam.  Solid foams can permit faster permeation of liquid reagents,
permitting the reaction throughout the whole material of a reagent
that would otherwise only be able to reach the surface, and can also
have superior mechanical properties in some applications.


Coating
-------

The simplest way to alter such objects' material properties is to coat
them with a liquid or gel which then adheres to them.  For example,
portland-cement mortar, calcium-aluminate mortar, magnesium-phosphate
mortar, shellac, wax, sodium silicate, paint, or glued paper.  Often a
shell of a hard, dense, or tensilely-reinforced material around a
lightweight, rigid core provides better tradeoffs of strength and
weight, and sometimes even better absolute resilience to impacts, than
either material could alone.

The material in my note on [globoflexia](globoflexia.md) goes into
considerably more detail about this family of construction, as well as
the note in Dercuano about sandwich panels.


Infiltration
------------

Plaster is fairly porous, lime a bit less so unless you find some way
to foam it.  You can make plaster even more porous by the expedient of
making it with more water.  These porous substances can have their
pores infiltrated with other substances, such as polymerizing resins;
vacuum or high pressure may be a useful way to make this infiltration
more complete.

Other useful infiltration materials might include thermoplastics,
paraffin wax, molten sulfur, and reagents for replacement reactions.


Replacement reactions
---------------------

As I mentioned in Dercuano, there are many anions that [precipitate as
water-insoluble solids][0] ([chart][29]) when confronted with
polyvalent cations such as calcium, magnesium, iron, copper, zinc,
titanium, manganese, boron, or aluminum.  Anions with such behavior
include phosphate, carbonate, alginate, silicate, sulfide, and also
usually hydroxide and occasionally oxalate or sulfate (with barium or
calcium).

[0]: https://en.wikipedia.org/wiki/Solubility_table
[29]: https://en.wikipedia.org/wiki/Solubility_chart

In the note there on “Likely-feasible non-flux-deposition powder-bed
3-D printing processes”, I suggested using binder jetting to exploit
these reactions to instantly cement powders.  But a possibly even more
interesting possibility is using such reactions to change the
properties of an already-existing object made from, for example,
plaster of Paris or lime.  For example, tadelakt is made by
precipitating calcium stearate (etc.) in a lime surface from soluble
stearates (etc.) such as that of sodium or potassium, then burnishing
the surface; thus lime is waterproofed.

It might be reasonable to use other semi-soluble or semi-solid sources
of polyvalent cations as well, such as gelatinous aluminum hydroxide,
or iron metal in the case of phosphate conversion coating.


### Phosphates ###

There are lots of [phosphates][6], many water-insoluble, hard, and
refractory, even without getting into pyrophosphates, metaphosphates,
and polyphosphates.  A wide variety of [phosphate minerals][11] exist
in nature.

[6]: https://en.wikipedia.org/wiki/Template:Phosphates
[11]: https://en.wikipedia.org/wiki/Phosphate_mineral

I’ve gone into some details on possible combinations in the note
mentioned above.  Perhaps you could, for example, apply a solution of
diammonium phosphate, monoammonium phosphate, or trisodium phosphate
to an object made of plaster of Paris, calcium hydroxide, or calcium
carbonate, and thus get a harder object made partly or wholly of
calcium phosphate.  The ammonium-carbonate combinations seem
particularly appealing, since it can be thermally decomposed to fairly
harmless materials.  [Phosphoric acid probably would not work on
plaster of Paris][1], and although it reacts with calcite (same
source), I’m not sure it *strengthens* the material in the process.

[1]: https://hal.archives-ouvertes.fr/hal-01634396/document "Stabilization of minerals by reaction with phosphoric acid"

One thing that does *not* work is ordinary hardware-store aqueous
phosphoric acid at room temperature and pressure; after a couple of
days no change was observable, and the plaster crumbled just as easily
as before.  This is in retrospect completely obvious: commercial
phosphoric acid is prepared by precisely this reaction, in reverse.
I've now gradually added enough baking soda to mostly convert the
phosphoric acid into some kind of gel; we'll see if the phosphate of
soda presumably now dominating the scene is any more effective at
phosphating the calcium.

Gelatinous aluminum hydroxide is another appealing target for this
kind of phosphate replacement reaction, since it is so easily molded;
perhaps it would yield extremely insoluble and refractory aluminum
phosphate (whether berlinite or in some other form, most likely an
amorphous one), along with either caustic soda or easily-boiled-off
aqueous ammonia.  Maybe some such reaction would be useful for
preventing neutral-electrolyte [aluminum-air
batteries](aluminum-air-batteries.md) from getting clogged up with
slime.

#### Magnesium phosphates ####

Although as a mineral its Mohs hardness is 6, magnesia or periclase is
a cheap “crushable ceramic” commonly used for electrical insulation of
heating elements due to its high thermal conductivity, and
[experiments have been done reacting it in this way with monoammonium
phosphate][2]; they explain:

> Phosphate cements possess mechanical and chemical properties that
> are superior to those of ordinary hydraulic (Portland) cements, …
> The reaction between a reactive form of magnesia and acid ammonium
> phosphate is very rapid and exothermic, and the materials cannot be
> practically used as such. Thus, the use of calcined or deadburned
> magnesia is suggested.

This is music to my ears, since of course instantly setting cements
are precisely what I most want for 3-D printing.  Also, they mention
that struvite, the very soft ammonium magnesium phosphate that I
feared would be formed from such reactions, does in fact form, but
*decomposes to monomagnesium hydrogen phosphate at 55°* by losing its
water and ammonia!

To retard the setting and permit molding, and in particular to avoid
increases in temperature that would be fatal to their
waste-immobilization purpose, they include boric acid as a setting
retardant.  They also included sodium tripolyphosphate, to increase
strength and reduce porosity, sand, and grinding dust (probably mostly
aluminum oxide and steel, with significant amounts of fiberglass); the
monoammonium phosphate:water:magnesia relationship seems to have been
3:2:4, probably by weight.

They report final compressive strengths in the 20–40 range (MPa, I
assume), and tensile strengths in the 1–2.5 MPa range.

[2]: https://www.scielo.br/pdf/mr/v12n1/05.pdf "Performance Analysis of Magnesium Phosphate Cement Mortar Containing Grinding Dust, Ribeiro & Morelli, Materials Research, Vol. 12, No. 1, 51-56, 2009"

[Another 2011 paper][3] explains:

> Magnesium phosphate cements (MPCs) have been extensively used as
> fast setting repair cements in civil engineering. They have
> properties that are also relevant to biomedical applications, such
> as fast setting, early strength acquisition and adhesive
> properties. However, there are some aspects that should be improved
> before they can be used in the human body, namely their highly
> exothermic setting reaction and the release of potentially harmful
> ammonia or ammonium ions...

They also used borate (as sodium borate) as a retardant, and also used
larger grains of phosphate salt.  They reported that monosodium
phosphate rather than phosphates of ammonium gave an amorphous result
instead.

Very interestingly, this also mentions “apatitic calcium phosphate
cements”, which have been investigated by the same authors and others
as possible bone cements.

[3]: https://pubmed.ncbi.nlm.nih.gov/21147277/ "Novel magnesium phosphate cements with high early strength and antibacterial properties, Mestres & Ginebra, 10.1016/j.actbio.2010.12.008"

[Yet another paper, this one in 2015, reports on 3-D printing][4]:

> Strontium ions (Sr²⁺) are known to prevent osteoporosis and also
> encourage bone formation. Such twin requirements have motivated
> researchers to develop Sr-substituted biomaterials for orthopaedic
> applications.  …developing Sr-substituted Mg₃(PO₄)₂-based
> biodegradable scaffolds. … powder printing, followed by high
> temperature sintering and/or chemical conversion…. strength
> properties of 36.7 MPa (compression), 24.2 MPa (bending) and 10.7
> MPa (tension) were measured.

They were using powdered trimagnesium diphosphate with strontium
replacing varying amounts of magnesium (up to ⅓), sintering it,
crushing it, 3-D printing it with a bit of
hydroxypropylmethylcellulose, depowdering it, sintering it again, and
then soaking it in diammonium phosphate to post-harden it.

[4]: https://www.researchgate.net/profile/Sourav_Mandal6/publication/285361634_Strength_reliability_and_in_vitro_degradation_of_three-dimensional_powder_printed_strontium-substituted_magnesium_phosphate_scaffolds/links/56dfd25408ae9b93f79aa142/Strength-reliability-and-in-vitro-degradation-of-three-dimensional-powder-printed-strontium-substituted-magnesium-phosphate-scaffolds.pdf "Strength reliability and in vitro degradation of three-dimensional powder printed strontium-substituted magnesium phosphate scaffolds"

See also below about zinc phosphate dental cement.

#### Phosphates of zinc, manganese, and iron ####

[Phosphate conversion coating][5] coats steel with water-insoluble
phosphates of these three metals by taking advantage of their
solubility in acid, such as (of course) phosphoric acid.

Ferric phosphate is what protects the Iron Pillar of Delhi, and also
some of my girlfriend’s kitchen pans, from rusting, despite its
porosity.  It can be achieved using nothing more than phosphoric acid.
Wikipedia leads me to believe that it should be orange to brown, but
mixing hardware-store phosphoric acid “converter” with powdered orange
rust gives a black insoluble compound instead, and so too is the
coating on the pans produced by boiling Coca-Cola in them.

Zinc phosphate is sometimes deposited on steel in the same way; the
steel reduces the hydrogen ions at its surface, precipitating zinc
phosphate out of solution.  It’s also used together with magnesium
phosphate as a dental cement; zinc oxide and magnesia are mixed with
phosphoric acid on a glass plate to allow them to cool, giving a pot
life of a few minutes.  [Wikipedia explains][7]:

> Zinc phosphate dental cement is one of the oldest and widely used
> dental cements. It is commonly used for luting permanent metal and
> zirconium dioxide restorations and as a base for dental
> restorations. Zinc phosphate cement is used for cementation of
> inlays, crowns, bridges, and orthodontic appliances and occasionally
> as a temporary restoration.
> 
> It is prepared by mixing zinc oxide and magnesium oxide powders with
> a liquid consisting principally of phosphoric acid, water, and
> buffers. It is the standard cement to measure against. It has the
> longest track record of use in dentistry. It is still commonly used;
> however, resin-modified glass ionomer cements are more convenient
> and stronger when used in a dental setting.

[5]: https://en.wikipedia.org/wiki/Phosphate_conversion_coating
[7]: https://en.wikipedia.org/wiki/Zinc_phosphate

[Manganous phosphate][8] is used similarly for metal protective
coatings.  Natural paragenetic combinations with iron phosphate
include [triplite][9] (Mohs 5–5.5), [triploidite][10] (Mohs 4.5–5),
and [purpurite][12] (Mohs 4–5, without iron).  Another [score of other
minerals include manganese and phosphate][13].

[8]: https://en.wikipedia.org/wiki/Manganese(II)_phosphate
[9]: https://en.wikipedia.org/wiki/Triplite
[10]: https://en.wikipedia.org/wiki/Triploidite
[12]: https://en.wikipedia.org/wiki/Purpurite
[13]: https://en.wikipedia.org/wiki/Manganese_phosphate

All three of these relatively hard phosphates, or families of
phosphates, can reasonably be formed by reacting phosphoric acid with
the respective oxides, which are easy to prepare and acquire, and
relatively inert (except, of course, for the heptoxide of manganese.)
I suspect that other soluble phosphate salts would also work as
phosphate donors.  Most of the oxides are soft materials that are easy
to shape and even cast, though solid pyrolusite (dioxide of manganese)
is 6–6.5.  In the form of fine powders with a little binder, the
materials might be more easily shaped before being bonded with a
phosphate donor.

Other possible cation-donating solids include the hydroxides (more or
less equivalent to the oxides, if we’re talking about aqueous
reactions) and the chloride of zinc.

#### Phosphate of boron ####

[Boron phosphate][15] is a somewhat refractory material, subliming
above 1400°, and water-insoluble in its crystalline form.  However,
both the reaction and the crystallization seem to be fairly slow at
room temperature.

[15]: http://acta-arhiv.chem-soc.si/46/46-2-161.pdf

#### Phosphates of zirconium ####

There is a very interesting [monozirconium diphosphate][16] but I
suspect that zirconia will not yield it easily.  You could surely
deposit zirconium nitrate on an inert surface, wash it with aqueous
lye to produce mostly insoluble zirconium hydroxide, and react that
with phosphoric acid; there might be easier routes.

[16]: https://en.wikipedia.org/wiki/Zirconium_phosphate

#### Etc. ####

Copper?  Titanium?


### Carbonates ###

Perhaps you can convert plaster of Paris to the harder lime, once
shaped, by one of the following approaches:

1. Soak it for a long time in sodium bicarbonate.
2. Or sodium carbonate.
3. Hell, or wash it heavily in aqueous ammonia.  Or lye.
4. Or soak in ammonium carbonate, then heat it up above 58° to drive
   off the ammonia.
5. Or wash it heavily with aqueous ammonium carbonate.
6. Or blast it with hot CO₂, which seems like maybe the most likely
   approach.

I’m not confident that any of these will work.  [Hot CO₂ works to
convert the sulfide of calcium into calcium carbonate][26], releasing
sulphuretted hydrogen, but you cannot convert plaster of Paris into
the sulfide as far as I know.  Heating the plaster past 1400° in air
will outgas vitriol, leaving behind lime, which is so much smaller
that it tends to fall apart.  Perhaps heating it to a somewhat lower
temperature in a CO₂ atmosphere, particularly under high pressure,
would work better; and perhaps it would help if something else were
removing vitriol from the gas chamber.

[26]: http://pubsapp.acs.org/subscribe/journals/tcaw/11/i01/html/01chemchron.html?

A process that would more likely work: carbothermically reduce the
sulfate to the sulfide, perhaps with carbon plasma, carbon monoxide,
or ethylene, rather than solid carbon, and then blasting the sulfide
with hot carbonic acid gas to liberate sulphuretted hydrogen and
produce the carbonate.

This is not the kind of tranquil process of painting on some kind of
conversion liquid that I was hoping for.

Lots of *other* polyvalent cation donor materials can productively
form insoluble carbonates, though.  Barium, copper, iron, lead,
manganese, nickel, and zinc, for example.


### Alginates ###

I haven’t seen a whole lot about alginates except for the usual
dental-mold and spherification stuff, using soluble sodium alginate
and insoluble calcium alginate.  I imagine that most candidate
polyvalent cations would work to coagulate the stuff.  In particular,
though, washing lime or plaster of Paris with a solution of sodium
alginate ought to give you a waterproof surface, similar to tadelakt.


### Silicates ###

Presumably washing the surface of lime or plaster of Paris with
soluble silicates such as those of sodium or potassium would
strengthen and waterproof the surface, and perhaps also improve its
refractory properties.  By applying these solutions to an open-cell
foam, perhaps the change could be usefully obtained throughout the
material.

As with phosphates, the possibilities of aluminum anions here are
tantalizing: can you mold something out of gelatinous aluminum
hydroxide, then harden it with sodium silicate?  But the silicates of
aluminum are enormously varied, ranging from kaolin and zeolites to
mullite.

One form of natural magnesium silicate, with a 3:4 Mg:Si ratio, is
talc, itself very easily carved, even with thumbnails, before being
fired to hardness.  [Synthetic magnesium silicate][18], for example as
a food additive or a plastic filler, is routinely precipitated in
amorphous form by mixing sodium silicate with the nitrate, chloride,
or sulfate of magnesium.

[18]: https://en.wikipedia.org/wiki/Synthetic_magnesium_silicate

Another form of natural magnesium silicate, with a 2:1 Mg:Si ratio, is
forsterite olivine, including the gemstone peridot.  Olivine is a
spectrum between forsterite and the silicates of iron (fayalite) and
manganese (tephroite).  Forsterite is just as hard as quartz and
considerably more refractory; forsterite melts at 1890°, [fayalite at
merely 1205°][19], and [tephroite at only 1345°][20].

[19]: https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1029/JZ072i016p04235 "Melting of fayalite up to 40 kilobars, Hsu, 1967"
[20]: https://pubs.usgs.gov/bul/1144d/report.pdf "Melting and Transformation Temperatures of Mineral and Allied Substances, USGS Bulletin 1144-D, 1967"

So you could imagine that a sufficiently small amount of silicate
added to a concentrated source of somewhat soluble magnesium, such as
magnesia, would produce forsterite, or an amorphous polymorph thereof.
The carbonate of magnesium (magnesite, Mohs 3.5–4.5) is some four
times as soluble as the oxide, and the fluoride (sellaite, Mohs 5–6) a
little less soluble than the oxide.


### Borates ###

Boric acid can form insoluble, hard, and sometimes refractory borates
of many polyvalent cations, as well as the water-soluble borax; worth
mentioning are chambersite: Mohs 7 (manganese); [boracite][14]: Mohs
7–7.5 (magnesium); [suanite][23]: Mohs 5.5 (also magnesium); and
[hilgardite][21]: Mohs 5 (calcium and chloride).  [Other borate
minerals are known][22].

[14]: https://en.wikipedia.org/wiki/Boracite
[21]: https://en.wikipedia.org/wiki/Hilgardite
[22]: https://en.wikipedia.org/wiki/Category:Borate_minerals
[23]: https://en.wikipedia.org/wiki/Suanite

Really though I suspect that the most promising thing to do with
borates is to burn them into [boria][25] or to somehow convert them into
boron nitride.  [Ammonium borate][24] seems like the ticket:

> * 10.9% soluble by weight at room temperature
> * stable to about 230°F (110°C), at which point it loses all but two
>   moles of water. If heated sufficiently, it releases the balance of
>   its hydration water and decomposes to boric oxide and ammonia.

(Though boric acid decomposes to boria at 300°.)

[24]: https://www.borax.com/products/ammonium-pentaborate
[25]: https://en.wikipedia.org/wiki/Boron_trioxide

### Sulfides ###

Generally the sulfides have the problem that they slowly decompose to
produce sulphuretted hydrogen and sulfuric acid, given access to moist
air.  [Carbothermic reduction of plaster of Paris produces calcium
sulfide][27].

[27]: https://en.wikipedia.org/wiki/Calcium_sulfide

### Fluorosilicates ###

Toxic [ammonium fluorosilicate][17] is reasonably water-soluble, as
are the fluorosilicates of copper, ferrous iron, lead, lithium,
manganese, and magnesium, but the fluorosilicates of barium and
calcium are much less so.

[17]: https://en.wikipedia.org/wiki/Ammonium_fluorosilicate


### Oxalates ###

Oxalates of soda, potassa, and ammonium are fairly water-soluble,
while oxalates of magnesium, silver, scandium, iron, and barium are
practically insoluble, and the oxalates of lime, copper, and zinc are
almost totally insoluble.  WP says the oxalate of lime starts to
decompose at 200°, though, so it’s not very heat-stable — but what it
decomposes *to* is, I think, the carbonate.  [It looks like that’s
right][28], but the temperature is around 500°, not 200°.  The
magnesium oxalate, similarly, decomposes to the carbonate between 420°
and 620°.

[28]: https://www.perkinelmer.com/lab-solutions/resources/docs/APP_Decomposition_Calcium%20Oxalate_Monohydrate(013078_01).pdf

To take a particular example, the oxalate of potassa [(LD₅₀ 660 mg/kg
orl-rat)][38] dissolves 36.4 g/100 mℓ water at 20°, the sulfate of
potassa 11.1 g, the oxalate of lime 670 μg, and the sulfate of lime
255 mg.  This suggests that a solution of 10% potassium oxalate will
eventually convert plaster of Paris into >99% insoluble oxalate of
lime, which can then be gently heated to get limestone.

[38]: https://www.fishersci.ca/shop/msdsproxy?productName=P273250&productDescription=potassium-oxalate-monohydrate-crystalline-certified-acs-fisher-chemical-2


### Fluorides ###

Bernd Jendrissek very graciously pointed out that
a fluoride replacement reaction is
commonly used to harden teeth and make them more acid-resistant by
converting hydroxyapatite to fluoroapatite.  The fluoride of calcium,
fluorspar, is both harder and less water-soluble than either its
sulfate or its carbonate, and so a double metathesis with a soluble
fluoride salt such as sodium fluoride might plausibly work to harden
plaster bodies.  These salts are somewhat poisonous; NaF's LD₅₀ is
[about 100 mg/kg, so it's used as rat poison,][39] but also in
toothpaste and to treat osteoporosis.

[39]: https://en.wikipedia.org/wiki/Sodium_fluoride

Sodium monofluorophosphate, as used in some toothpastes, might be
another alternative, doing the phosphate conversion and fluorination
in a single step; its LD₅₀ is [about 500 mg/kg][40].

[40: https://en.wikipedia.org/wiki/Sodium_monofluorophosphate


Inert fillers and high-temperature activation
---------------------------------------------

There’s a fourth totally different approach to strengthening these
quasi-refractory calcium compounds, one that doesn’t involve
room-temperature gas-phase or aqueous reactions.

Both plaster of Paris and ordinary lime cement remain solid up to high
temperatures — plaster of Paris decomposes to quicklime above 1400°,
while fully carbonatated lime decomposes to quicklime starting much
colder, above 825°, but then quicklime itself remains solid to 2613°.
However, it may be in smaller pieces than the original shape, if the
changes in volume were enough to crack the shape.

One possible approach to the problem is to incorporate inert
needlelike material into the original plaster to bridge the gaps;
[mullite can be bought in crystals][30] for this purpose for making
pottery, or [as polycrystalline fibers for foundry linings][31].  At
lower temperatures, wire of steel, copper, or stainless steel can
work.  Plant fibers, such as sisal, sawdust, or used yerba mate, char
between 200° and 300°; but the charcoal can survive and continue to
add significant strength up to much higher temperatures than the rest
of the components, unless oxygen burns it out first.

Even ordinary quartz sand may help.  Suppose your plaster mix is 90%
quartz sand and 10% plaster of Paris binder, and when you heat the
plaster enough to dehydrate it, the [plaster shrinks by 0.2%][34]
linearly (0.6% in volume).  But, since each linear dimension is only
about 1.2% plaster binder across most of the perpendicular
cross-sectional area, the linear shrinkage is only 0.0024% instead of
0.2%.  This can make the difference between heat cracking the material
and not.

(Quartz is not the ideal material because [it dunts at 573°, expanding
0.45%][35].  [Many other tempers are used in pottery to improve this
situation][36], crushed-brick grog being one of the most common.)

[30]: https://digitalfire.com/material/mulcoa+70+mullite
[31]: https://www.industrialheating.com/articles/91900-advantages-of-mullite-fiber-linings-for-high-temperature-furnaces
[34]: https://pubs.rsc.org/-/content/articlelanding/1964/tf/tf9646001947/unauth#!divAbstract
[35]: https://en.wikipedia.org/wiki/Quartz_inversion
[36]: http://www.kgs.ku.edu/Publications/Bulletins/211_4/bauleke.html

However, we can go further and actually use fillers that will actually
*react with* the calcium compounds at high temperature.  I saw this on
a sciencemadness thread, but I don’t know who to credit: for example,
you can incorporate an “inert” filler such as rutile, which at 1300°
and above will stop being inert and combine with the quicklime to form
[calcium titanate][32] (melting point 1975°).  Even silica,
particularly in an amorphous form such as infusorial earth, or as
soluble silicates, might work for this; [larnite melts at 2130°][33].
And clays could provide both alumina and silica.

[32]: https://en.wikipedia.org/wiki/Calcium_titanate
[33]: https://en.wikipedia.org/wiki/Calcium_silicate

(Titanate also forms mineral salts with manganese, magnesium, barium,
lead, zinc, iron, and half a dozen other metals.  These are basically
all insoluble, even the metatitanate of soda, but [supposedly there’s
a water-soluble triethanolamine titanate][37].)

[37]: https://patents.google.com/patent/US4621148A/en
