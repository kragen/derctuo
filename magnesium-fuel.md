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
molar mass is 18.01528(33) g/mol.  Magnesia's are -601.6 ±0.3 kJ/mol
and 40.304 g/mol (compared to, say,
-1675.7 kJ/mol and 101.960 g/mol for [alumina][8],
nearly the same energy density);
although I'm not very sure of my understanding of
the thermochemistry, I think this means that splitting the water sucks
up about half of the heat you'd otherwise get out of the reaction,
since both MgO and H₂O have a single oxygen, so a mole of H₂O produces
a mole of MgO; so you would need about twice the amount of magnesium
to produce a given amount of energy.

[4]: https://en.wikipedia.org/wiki/Properties_of_water
[8]: https://en.wikipedia.org/wiki/Aluminium_oxide

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

Maintaining the magnesium fuel in large solid pieces until near time
to use would be a useful safety measure.  These would be much harder
to ignite accidentally.  Perhaps the simplest approach would be a
round magnesium rod that twists in a device exactly like a manual
pencil sharpener to shave off shavings of a calibrated thickness.

Under some circumstances, it might be best to first preheat some
magnesia by burning magnesium, with little or no gas release, and then
use a second, later burst of gas to move the generated heat to where
it's needed.  This decouples the time during which the combustion
happens — which may be limited by, for example, considerations such as
the one mentioned above of burning the magnesium to solid magnesia
fast enough that it doesn't melt into a round mass with little surface
area, or inversely by the inability to burn the magnesium as fast as
would be desired because of limited surface area — from the time
during which the heat is transferred to where it will be used, which
might be shorter or longer than the combustion time.

Recharging spent magnesium fuel should be considerably easier than the
analogous process for aluminum, which is especially interesting for
use as a motor vehicle fuel.  Something like three fourths of
magnesium today is produced in China by the Pidgeon silicothermic
process, boiling magnesium vapor at sub-atmospheric pressures out of
mixed MgO and ferrosilicon powders at 1200°–1400°, and further
stabilizing the silica byproduct with CaO.  However, the historically
dominant process was electrolysis of molten MgCl₂ produced from HCl
and MgO; the electrolysis releases the Cl₂, which can be
exothermically recombined with H₂ with ultraviolet light.  Pure MgCl₂
melts at 714°, but, e.g., Davy fluxed it with corrosive sublimate to
discover magnesium at a tamer temperature; so a recharging apparatus
of a reasonably small size and temperature might be feasible.

[A new, more direct process][7] uses a solid zirconia electrolyte to
directly electrolyze MgO at 1150°–1300°, in order to drop the cost of
magnesium for *structural* applications in vehicles.  The cathode is a
bath of molten MgO through which argon is bubbled, coming out
containing Mg vapor.  The O₂ can travel through the zirconia to the
cathode, made, for example, of molten copper, tin, or silver, or of a
zirconia–nickel cermet coating on the zirconia.  Magnesia-stabilized
zirconia is more stable in the molten salt bath, but lower
conductivity; they found some kind of sooper seekrit ingredient to
keep the molten MgO from corroding regular yttria-stabilized zirconia.
Like the Pidgeon process, the magnesium produced is in vapor form, and
so a distillation step inherently purifies the reaction product.
(With appropriate "fluxes" or molten-salt solvents, this same SOM
process has been used to smelt iron, silicon, tantalum, and titanium.)

[7]: https://web.archive.org/web/20131113035743/http://www1.eere.energy.gov/vehiclesandfuels/pdfs/merit_review_2011/lightweight_materials/lm035_derezinski_2011_o.pdf "Solid Oxide Membrane (SOM) Electrolysis of Magnesium, Powell et al., 2011"
