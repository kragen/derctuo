Thinking a bit about thermite, it occurred to me that, for sand
casting or investment casting of metal objects on the scale of
centimeters, it might be best to generate the metal object right on
top of the mold, thus avoiding the necessity to open a hot furnace,
carry a red-hot or white-hot crucible, and pour the crucible.  Copper
in particularly is notoriously tricky to cast in this way.

Microwave oven kilns
--------------------

The first version of this process I saw was back in the 1990s with
microwave-oven casting: some guy whose name I forget stuccoed his clay
lost-wax mold with magnetite and graphite as susceptors, taped over
his microwave-oven fan, and microwaved the assembly until it was hot
enough to melt metal.  A more recent incarnation of a similar idea
used a microwave-oven-sized tiny porous-insulating-refractory kiln
with charcoal inside of it to calcine magnesia to make a
magnesium-oxychloride knife from mostly seawater; the refractory is
transparent to microwaves, and avoids the necessity to cover up the
ventilation fan, but the charcoal picks up the microwaves.  The stucco
guy explained that magnetite works better than graphite, but only up
to its Curie temperature, at which point graphite starts to work
better because of its higher resistance; presumably less pure forms of
carbon such as charcoal have higher resistivities and therefore work
down to lower temperatures as well.

I've also had success igniting arcs between pieces of charcoal and
steel wool inside a microwave oven, and such arcs would also work to
heat up the interior of an insulated ceramic space like that.  (I also
left a glass of water elsewhere in the microwave to limit the risk of
magnetron overheating in case my susceptors proved too reflective.)
Peepholes in an optically-opaque insulating refractory kiln, whether
for use in a microwave oven or not, might permit the pyrometric
inspection of the blackbody interior when the microwave and thus the
arc is turned off.  (I was doing the arcs out in the open on top of a
bed of granulated salt, because I had no sand; the molten salt globs
were easier to remove from the granulated salt than the molten glass
globs were from the glass floor of my microwave oven.)

(Silicon carbide is another susceptor with a wider temperature range
than either magnetite or graphite.)

Thermite
--------

If you can ignite thermite (whether by arcs, Joule heating, or by any
other means), in a sand funnel scooped out of the top of a
metal-casting mold, then you can presumably fill the mold with the
liquid metal produced, whether that is iron from an aluminum/hematite
thermite or copper from an aluminum/cuprite thermite.  Moreover, if
the reaction chamber is sufficiently refractory for the reaction not
to melt through its bottom, it can be used to heat an insulated
reaction chamber rapidly to 2500° to 2800°, a temperature that can be
calibrated by the thermite's stoichiometry rather than regulated by
feedback, and which may be useful for other reactions that are
difficult to perform at more convenient temperatures, such as the
graphitization of amorphous carbon foam, carbothermic reduction of
metals with refractory oxides, and so on.

(This process is routinely used with graphite crucibles or sand molds
for welding copper conductors and railway tracks.)

Here in Argentina, on Mercado Libre, hematite ("pigmento oxido de
hierro rojo") costs US$4 to US$6 per kg, while magnetite is in around
the same range.  I can't find cuprite; see [the note on copper
salts](copper-salts.md) for more.  Cupric oxide (tenorite) is much
easier to prepare in bulk, but the resulting thermite acts with deadly
rapidity; you might be able to reduce this menace by diluting the
thermite mix with less-active hematite, excess aluminum, copper
filings, iron filings, silica sand, or even — at the risk of producing
hot caustic gas — blue vitriol.  A small amount of excess aluminum and
perhaps hematite or iron should produce an [aluminum bronze] rather
than copper; CuAl10Fe3 is 8.5%-11% aluminum and 2%-4% iron, the
remainder being copper.  Aluminum bronzes are lighter, stronger, more
corrosion-resistant, and lower-melting than copper.

[aluminum bronze]: https://en.wikipedia.org/wiki/Aluminium_bronze

Any excess aluminum would necessarily require that the reaction vessel
be carefully purged to eliminate gaseous oxygen to reduce the risk of
an aluminum fire.  At ordinary temperatures carbon dioxide is not
sufficiently inert to escape this danger, although paradoxically above
about 2300° at atmospheric pressure the equilibrium goes the other
way, and carbothermic reduction of aluminum becomes possible.

In any case, mixing enough of the desired end product into the
thermite ought to tame it adequately, although thermite is plagued
with hazards stemming from surprising interactions accelerating its
action.

### Sulfur thermites ###

Sulfur is another possible oxidant for powdered metals, particularly
in a sealed pressure vessel.  This poses the risk of the sulfur
boiling if the reaction is not fast enough and pressure is not
contained, but it has a couple of very interesting benefits.  First,
it is possible to weld aluminum with this method, producing only
aluminum sulfide (melting point 1100°, well above aluminum's 660°;
very hard but decomposes in water to aluminum hydroxide and hydrogen
sulfide, from which the sulfur can easily be recovered).  Second, iron
pyrite is a beautiful and interesting material in its own right, quite
aside from its historical usage in starting fires and in semiconductor
diodes.  Finally, while metal oxides like those of iron, manganese,
and copper inevitably leave behind a residue of the reduced metal as
well as the molten oxide, sulfur can produce a pure molten metal
sulfide if the stoichiometry is correct.

### Welding and sintering ###

Such welds can in principle be executed by pressing a powder of the
thermite, or even just the oxidant, in between blocks or grains of the
solid material to be welded, then igniting the mass, thus forming the
weld.  Although the sulfides formed will weaken the weld, being as
they are weaker than the bulk metal (except perhaps in the cases of
aluminum and zinc), if the weld remains hot long enough for them to
spheroidize, the loss in strength may be minimal.  As I wrote in
Dercuano, it may be possible to use such a process as a way to rapidly
sinter a green body primarily composed of metal grains.

(I suspect it's possible to weld magnesium by this method as well, a
task which is challenging in general and impossible with ordinary
thermite.)

Applications of thermites to 3-D printing
-----------------------------------------

By selectively depositing a small amount of oxidant in a bed of metal
powder which is then suffused with an inert gas before ignition, as I
wrote in Dercuano, it should be possible to 3-D print near-net
mostly-metal objects with a rough surface and a small amount of oxide
or sulfide trapped in spheres deep inside of them.  Moreover, this
should also be possible with selective deposition of a paste
consisting mostly of metal powder with a small amount of oxidant and
binder; the oxidant might be liquid sulfur or amorphous sulfur, in
which case no extra binder would be needed; or the oxidant might be
crystalline sulfur or oxides, hydroxides, or nitrates of metals, in
which case the binder would be some other material such as an aqueous
bentonite colloid or molten lead-tin solder.  A third 3-D printing
process would involve selectively jetting a binding agent, perhaps
just water, into a powder bed deposited layer by layer, followed by
the depowdering of the green body in the way that is currently usual
for such powder-bed processes; then the green body would be ignited,
perhaps after drying.

In all of these 3-D printing processes, you could use inert, dense,
high-boiling, and endothermic fillers to reduce the tendency of the
ignited body to evolve gas and blow itself apart; incorporating
adequate porosity into the design would also help.  Silica, silicon,
lithium, phosphoric acid, olivine, lead, tungsten, and many other
substances could be useful here.  Sodium chloride and alumina are [in
use for this purpose today][44].

[44]: http://web.archive.org/web/20200220060831/ism.ac.ru/handbook/shsf.htm

Other ways to heat a pocket furnace
-----------------------------------

Other ways to heat a charge of metal in an insulating chamber
immediately atop a mold, so as to drop it into the mold once it
finishes melting and immediately make a cast, include the use of a
small in-place carbon-electrode electric arc furnace (as demonstrated
by, for example, The King of Random, RIP); a small induction heating
coil, which can be placed outside the refractory chamber itself (I've
written about sealed insulated induction furnaces previously in
Dercuano); and optical heating through a peephole, either by a focused
laser or by focused sunlight, perhaps previously "collimated" by
passing it through a "pinhole" before focusing, in order to permit the
usage of smaller solar ovens.

Peepholes of transparent silica aerogel or aerogels of other
high-temperature ceramics, such as yttria or yttralox, may permit
pyrometry and optical heating without loss of heat to convection,
although at these temperatures radiative heat loss is probably more
significant.  They can also prevent contamination of the reaction
chamber by reactive gases such as oxygen, though perhaps purging the
vicinity or directly the chamber with other gases such as argon may be
more effective, or the loss of scarce reaction products such as vapors
of gold or mercury.

A more everyday way to melt refractory metals and reduce ceramics less
refractory than tungsten is to heat them with a carbon arc in argon on
a water-cooled copper hearth, which can provide the necessary
grounding.  However, this approach is not very efficient due to the
high thermal losses into the copper, and might result in copper or
carbon contamination of the melt.

Zirconium-based ceramics
------------------------

Another potentially interesting powder-bed 3-D printable end product,
which I didn't appreciate at the time, is the possibility of printing
in yttria-stabilized cubic zirconia.  This could be done either by
sintering or fusing a bed or green body of powder of the ceramic with
any of the thermite compositions described above, or, more
outlandishly, by selectively oxidizing a bed or green body of
*metallic zirconium* powder (just US$12–33/kg from China in 2015-19,
[according to the USGS]!) with the appropriate percentage of yttrium
present (about 10% on an yttria basis, ideally already as the
trivalent oxide, [US$3–8/kg][39], but conceivably just as the metal,
US$34–48/kg, or as some other salt such as the acetate, formate,
nitrate, or sulfate, all of which are water-soluble; or as the
hydroxide).  The oxidation process would inevitably leave bits of the
reduced oxygen-donating metal behind, probably trapped inside the
zirconia mass, probably weakening it somewhat and potentially cracking
it.  (The candidate donor metals — iron, copper, chromium, and so
on — are strong, hard, and tough, but cubic zirconia is much harder,
so it cannot transfer mechanical loads to them unless it cracks.)

[according to the USGS]: https://www.usgs.gov/centers/nmic/zirconium-and-hafnium-statistics-and-information
[39]: https://pubs.usgs.gov/periodicals/mcs2020/mcs2020-yttrium.pdf

No such concern about donor metal remnants applies for oxidation to
zirconium *carbide*, which can perhaps be done with just zirconium and
carbon; however, the temperature is probably high enough to melt any
remaining zirconium, so depositing a bit of zirconium into a bed of
graphite or diamond dust might be better than vice versa.  Zirconium
carbide is even more refractory than zirconia (to the point that I
doubt the above-mentioned thermites can sinter it), but its standard
enthalpy of formation is smaller, only -207 kJ/mol to zirconia's -1080
kJ/mol and [alumina]'s -1675.7 kJ/mol; so it's conceivable that it
might need a boost to fully fuse upon ignition.  Analogous
considerations apply to [zirconium boride], for which this process is
already in use, under the name "[self-propagating high-temperature
synthesis]".

One clever trick from zirconium boride SHS, probably applicable to
this entire class of processes, is to include metallic magnesium in
the mix to capture unwanted oxygen from the feedstocks, preventing it
from outgassing; the magnesia can then be removed with mild acid
leaching after cooling.  Glucina might work better for such oxygen
immobilization in the sense of occupying less volume, and it is harder
than magnesia, though slightly less refractory; but removing it later
requires more aggressive chemicals, and of course it is considered
very toxic.

[alumina]: https://en.wikipedia.org/wiki/Aluminium_oxide
[zirconium boride]: https://en.wikipedia.org/wiki/Zirconium_boride
[self-propagating high-temperature synthesis]: https://en.wikipedia.org/wiki/Self-propagating_high-temperature_synthesis

Cubic zirconia is the stable structure for zirconia above 2370°, so in
the case of producing zirconia it is probably necessary for the
temperature to exceed this level.  Candidate alternative stabilizing
dopants include calcium, titanium, and magnesium; calcium and
magnesium oxides in particular might be particularly easy to handle,
and [they provide superior mechanical properties to yttria!][40]
However, historically sintering them has been too difficult, a problem
this thermite-printing process might solve; they also have worse
high-temperature stability than traditional yttria-stabilized zirconia.

[40]: https://www.preciseceramic.com/blog/advantages-of-yttria-stabilized-zirconia-ysz-compared-to-other-stabilizers/

Including aluminum in the zirconium mix might offer some additional
advantages.  I think it won't give you hotter results — while I
*think* aluminum has a higher [energy density] of 83.8 MJ/ℓ than
zirconium; alumina's molar mass is 101.960 g/mol, while zirconia's is
123.218 g/mol, giving respectively standard enthalpies of formation of
-16.4 MJ/kg and -8.8 MJ/kg, advantage aluminum; however, while
alumina's specific heat is the low 0.88 J/kg/K, zirconia's specific
heat is a super-low 0.27 J/g/K, so I think zirconium as a thermite
fuel will actually get hotter than aluminum, though I think it's
entirely likely I'm misunderstanding how to apply the thermodynamics.
Also, though, the resulting alumina–zirconia composites offer [better
mechanical properties than either ceramic alone][51], being harder
than zirconia but tougher (higher tenacity) and consequently stronger
than alumina.

[energy density]: https://en.wikipedia.org/wiki/Energy_density#Tables_of_energy_content
[51]: https://ceramique-technique.com/en/materials/alumina-zirconia-composites-zta-atz

Several of these powder-bed processes might benefit from the powder
bed being pressed in a hydraulic press, as for hot isostatic pressing
but without the heat, at the time of ignition.  This would tend to
accelerate the reaction dangerously, but it might also diminish the
tendency for the reaction to blow the workpiece apart or produce
porosity, or for the heat produced to deform the workpiece.

Of course other similar metals, such as titanium, tantalum, hafnium,
niobium, vanadium, molybdenum and thorium, can be used instead of
zirconium to 3-D print similar [ultra-high-temperature ceramics][72]
in similar ways; titanium carbide and molybdenum boride, among many
other examples, have been made by SHS.  As another example, there’s a
1997 paper by Sundaram et al., that got titanium diboride by SHS of
magnesium, amorphous boria, and rutile.

[72]: https://en.wikipedia.org/wiki/Ultra-high-temperature_ceramics
