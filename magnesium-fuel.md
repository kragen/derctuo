Magnesium has an [energy density][0] of 43.0 MJ/ℓ and a specific
energy of 24.7 MJ/kg.  This is among the highest energy densities of
any easily burnable fuel — iron, polystyrene, polyethylene, and
lithium borohydride are similar, while the more difficult aluminum,
carbon, and silicon are up in the 70–84 MJ/ℓ range.  (See, however,
[the note on aluminum-air batteries](aluminum-air-batteries.md).)  It
excels iron at specific energy, and polystyrene, polyethylene, and
lithium borohydride excel it.  But burning polystyrene, polyethylene,
and lithium borohydride produces a lot of gas, spreading out the heat
a great deal.  So, for compact, easily ignited fuel to produce a high
temperature, magnesium is pretty much tops.  As a bonus, it’s pretty
abundant and easily electrowon from seawater.

[0]: https://en.wikipedia.org/wiki/Energy_density#Tables_of_energy_content

[Magnesia][1] has a molar mass of 40.3 g/mol and a heat capacity
around room temperature of 37.2 J/mol/K; dividing these two gives an
unremarkable specific heat of 0.923 J/g/K.  [Magnesium itself][2] has
a molar mass of 24.3 g/mol, so magnesia (MgO) is 60.3% magnesium;
burning a kg of magnesium yields 1.66 kg of magnesia, and, as
mentioned above, 24.7 MJ.  From this we can derive that, if its
specific heat remained constant, the resulting magnesia would be at
26500°, which means that in practice the upper limit to the
temperature will be imposed by heat loss mechanisms and the finite
speed of combustion, since this is several times hotter than the
surface of the sun.

[1]: https://en.wikipedia.org/wiki/Magnesium_oxide
[2]: https://en.wikipedia.org/wiki/Magnesium

Thus we have magnesium flashbulbs.

Consider a kilojoule.  We can store it in 23 microliters of magnesium
weighing 40 mg.  Liberating it requires another 26 mg of oxygen, for
example from the air, which contains it at about 210 mg/ℓ, so about
130 mℓ of atmospheric-pressure air are needed; the reaction can be
arranged to proceed at rates of anywhere from tens of watts or so up
to a megawatt by controlling the introduction of the air, as long as
the hot magnesium doesn’t start reducing its reaction chamber, or of
course melting it.  If it is necessary to carry the oxidizer as well,
water works well once the reaction is going, since water is 89%
oxygen; 26 mg of oxygen as water thus occupies 29 μl.  (See below,
though.)

This makes magnesium appealing as a compact way to store energy
capable of safe, controlled high-power release.  One of the few
examples of this being done in practice is the MAGIC engine developed
by Mitsubishi and Takashi Yabe and others at the Tokyo Institute of
Technology, which also used a water oxidizer;
Yabe has also worked on magnesium-air fuel cells.

The oxygen-magnesium reaction produces no gaseous products unless the
temperature is allowed to go very high (magnesia boils at 3600°,
though magnesium melts at 650°), but the water-magnesium reaction
produces hydrogen.  The MAGIC engine secondarily burns the hydrogen
produced in air to recover the enthalpy of formation of the water,
which was drawn from the initial water–magnesium reaction.  [Water's
standard enthalpy of formation][4] is -285.83 ±0.04 kJ/mol and its
molar mass is 18.01528(33) g/mol.  Magnesia's are 601.6 ±0.3 kJ/mol
and 40.304 g/mol; although I'm not very sure of my understanding of
the thermochemistry, I think this means that splitting the water sucks
up about half of the heat you'd otherwise get out of the reaction,
since both MgO and H₂O have a single oxygen, so a mole of H₂O produces
a mole of MgO; so you would need about twice the amount of magnesium
to produce a given amount of energy.

[4]: https://en.wikipedia.org/wiki/Properties_of_water

The [hydrogen][6] also soaks up 28.836 J/mol/K of heat, lowering the
potential maximum temperature further, but I think by another factor
of less than 2.  So we're still talking about maximum temperatures
that exceed magnesia's boiling point.

[6]: https://en.wikipedia.org/wiki/Hydrogen

(Under [appropriate conditions][5] you can generate hydrogen at room
temperature from magnesium and water.)

[5]: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5090637/

For controlling the reaction rate, the most appealing option would
seem to be preheating the magnesium to somewhat below its melting
point, then introducing the oxidizer at a controlled rate.  The
temperature will immediately rise enough to melt the magnesium, which
over a long enough timescale will reduce the available area for the
reaction to take place; but in many circumstances the reaction can be
run to completion on a shorter timescale than that, and the increasing
temperature may be an effective countervailing force.
