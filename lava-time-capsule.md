I was just watching a Long Now Short which described the work of
Tobias Kestel and Florian Puschmann, who flouted sacred taboos in
02009 to put time capsules into lava at Kilauea.

It occurred to me that, although putting time capsules into lava is a
straightforward procedure (though one with substantial risk of death),
making a time capsule that could survive within the lava is much less
so.  Tin–lead solder melts at 187°, paper and organic polymers char in
the 200°–400° range, lead itself melts at 327°, aluminum melts at
660°, ordinary soda-lime glass softens around 900° depending on the
precise mix, gold melts at 1064°, copper melts at 1085°, but Kilauea
erupts at 1170° and has lava tubes of 1250°.  Silicon semiconductors
exposed to such a temperature for more than a few seconds will suffer
degradation from dopant diffusion, and their aluminum or copper
conductors will melt and may bead up.

One obvious approach is to to use fused quartz, which doesn't soften
until 1600°.  But a possibly more interesting approach is to insulate
the time capsule so that it can survive until the lava cools; by
sheathing it in a thick layer of refractory firebrick, the heat flux
through its surface can be reduced, and by including some phase-change
thermal mass within it, the temperature can be maintained at a level
that doesn't damage the payload.

The most obvious candidate is water, but as I noted in [Desiccant
Climate Control](desiccant-climate-control.md) and [Muriate Thermal
Mass](muriate-thermal-mass.md), alabaster and muriate of lime also
offer substantial energy densities; alabaster in particular has the
advantage of forming an extremely cheap layer that can serve as
insulation once calcined, surviving in solid form until 1460°, at
which point it outgasses vitriol (see [the note on plaster
foam](plaster-foam.md).)  However, water's enthalpy of vaporization is
2.26 MJ/kg, and its boiling point is only 100° at normal pressures.

It's also important that the time capsule be small enough to be
covered entirely by lava, and dense enough that it doesn't float in
the lava.  This is in tension with the requirement for insulance;
foaming things to improve their insulance makes them less dense, and
plaster of paris is not very dense to start with (I'm finding
conflicting figures of 0.7–2.6 g/cc, but I think [pahoehoe is about
2.7–3 g/cc][4]).  A reasonable way to resolve this is to
counterbalance the less dense alabaster with something denser inside
the capsule, like steel (7.9 g/cc, [US$1.06/kg][3]), lead (11.34 g/cc,
[US$2.20/kg][1]), or zinc (7.1 g/cc, [US$2.76/kg][2]).  I think ‘a‘a
may have higher gas content and consequently be lighter.

[1]: library/mcs2020-lead.pdf "https://pubs.usgs.gov/periodicals/mcs2020/mcs2020-lead.pdf"
[2]: library/mcs2020-zinc.pdf "https://pubs.usgs.gov/periodicals/mcs2020/mcs2020-zinc.pdf"
[3]: library/mcs2020-iron-steel.pdf "https://pubs.usgs.gov/periodicals/mcs2020/mcs2020-iron-steel.pdf"
[4]: https://www.researchgate.net/publication/332382648_Petrophysical_variations_within_the_basaltic_lava_flows_from_Tural-Rajawadi_hot_springs_Western_India_and_their_bearing_on_the_viability_of_low-enthalpy_geothermal_systems

If we figure on a nominal lava density of 3 g/cc, steel gives us
-4.9 g/cc of effective weight (4.9 g/cc of buoyancy) and lead gives us
-8.3 g/cc, at prices of respectively US$8.37/liter and US$24.90/liter,
giving us prices per effective weight of US$-1.71/kg and US$-3.01/kg
respectively.  That is, to compensate for a kg of buoyancy due to the
low density of alabaster, you'd need US$1.71 worth of steel or US$3.01
worth of lead.

Lead has the potential advantage that it melts at 327° and thus forms
an extra protective phase-change thermal mass, though it would be a
poor tradeoff for water: [4.77 kJ/mol at 207.2 g/mol][5] gives
23 kJ/kg, 100 times less than what's needed to boil water.  However,
it has the advantage that, unlike water, it stays in the capsule after
changing phase — so it can resist not only the initial injection event
but also potential subsequent reheating events.  But its melting point
is too high to save paper, phenolic circuit boards, or ordinary solder
joints.

[5]: https://en.wikipedia.org/wiki/Lead

It's probably also important to protect the insulation from dissolving
in the lava — depending on its composition, the lava might react with
it.  For this purpose it may be best to can the entire time capsule,
insulating layer and all, in something that can withstand the lava; a
steel can is an obvious choice, since steel is pretty much good to
1400° and doesn't dissolve in lava.  But then there's the question of
how the steam will escape through the can; it needs to be porous
enough for the steam to get *out* but not so porous the lava will get
*in*, and moreover it needs to not clog with hardening lava (too
much) or the steam will build up inside.

The steam bubbling out through the lava may be sufficient to enlist
some of the lava around the capsule as additional insulation, and may
also be sufficient to keep open an air passage to the surface,
permitting the penetration of circadian air pressure variations (for
example, the tidal swings) until, at least, the next lava flow.  ‘A‘a
may be more favorable in this sense as well, since much of it cools as
open-cell foams; very commonly a lava flow will have a top surface of
porous foams such as scoria over a less porous base layer, and it may
be ideal to optimize the capsule's specific gravity to float at or
near the bottom of the porous layer.

Within the time capsule you can imagine a computing system that
communicates through the lava, for example using AM radio (a big
loopstick antenna can not only propagate waves through the steel can
but also harvest energy from broadcast radio stations as long as those
exist) or piezoelectric vibration.

I've previously looked around for mass-market archival media and found
that nearly all current electronic storage (Flash, FRAM, MRAM) is only
designed for 10-year data retention, occasionally 100-year.  So such a
time capsule would need an energy source to fight data loss with.

Aside from the obvious, but difficult, approach of using a betavoltaic
battery, and aside from energy harvesting from AM radio as mentioned
above, other candidate energy sources include: daily thermal cycling;
vibration; air pressure variation, like the Atmos clock; or some other
source.

I thought about a geomagnetic energy-harvesting system, but it seems
that it would either need to run at much lower powers than are
currently feasible, only run during violent geomagnetic storms, or be
many orders of magnitude larger than is feasible to sink into flowing
lava; see [Geomagnetic energy harvesting](geomagnetic-energy-harvesting.md).

How thick would the insulation need to be?  It depends on how long the
lava flow takes to cool down to a temperature where the water stops
boiling.  Suppose arbitrarily that our insulation is 200 mW/m/K, we
have 1000 kg of water for the 1200° lava to boil off, and our surface
area is 10 m².  Well, `(1000 kg 2.26 MJ/kg) / (1100 K * (200 mW/m/K) *
10 m^2)` gives us about 1.03 million seconds per meter, so if our
insulation is 1.03 microns thick, we only last a second, while if it's
a meter thick, we last almost 15 days.  So to last an hour we need
3.5 mm of insulation, to last 3 hours we need 10.5 mm, to last 8 hours
28 mm, to last 24 hours we need 84 mm, to last 48 hours we need
170 mm, and to last a week we need almost 600 mm.  (This is
discounting the thermal mass and possible phase changes of the
insulation itself, as well as all the thermal mass of the payload.)

I think this demonstrates that this design approach for a survivable
lava time capsule is feasible but probably would not fit in your hand.
