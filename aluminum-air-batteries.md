There is a very interesting device called the [aluminum-air fuel
cell][3]: a consumable aluminum anode (-1.662 V to the oxide, but the
relevant oxidation here is the oxidation to the hydroxide at -2.31 V),
separated from a porous carbon cathode by a thin porous insulating
material soaked with an electrolyte such as sodium chloride.  With a
caustic potash electrolyte this produces 1.2 V per cell, but table
salt provides a wholly acceptable 0.7 V.

Aluminum’s atomic weight of 26.981 538 4(3), its generous three
electrons per atom, the electron’s charge of 1.602 176 620 8 × 10⁻¹⁹
C, and Avogadro’s number of 6.022 140 9 × 10²³ atoms / mole together
give us 10.727 928 available coulombs per kilogram of aluminum, or
about 7 or 8 MJ/kg at 0.7 V.  This is a respectable fraction of
aluminum’s energy density as a fuel, 31 MJ/kg!  (When burned in
oxygen.)  It’s probably as good as you could expect from fueling a
steam turbine from aluminum and air, say.  This is astonishing because
batteries normally don’t come anywhere close to heat-engine
energy-density territory.

[3]: https://en.wikipedia.org/wiki/Aluminium%E2%80%93air_battery

Of course you can scale it down in a way that you can’t scale down a
steam turbine: a gram of aluminum should provide you with 7 or 8 kJ,
and only 13 mg of aluminum is necessary to provide 100 J.

Amateur aluminum-air batteries commonly use a copper current-collector
grid on the carbon cathode rather than nickel, but I suspect that will
suffer anodic corrosion to copper chloride over time.  Replacing the
copper wires when you replace the aluminum anode should not be too
hard, but neither is plating them in nickel, if you have some; maybe
lead and/or tin would also work.

Removing the gelatinous hydrated aluminum hydroxide may be more
difficult; maybe some sodium fluoride or monosodium phosphate would
work for that if they don’t corrode the aluminum fuel itself, but then
they become additional consumables.  (There’s also the possibility
that tridentate citrate complexes might help.)  Mixing some ethanol or
isopropanol into the electrolyte might encourage the hydroxide to
de-gel without creating *too* much toxicity or fire hazard.

I’ve been trying to figure out what kind of small generator would work
to provide, say, a laptop with long autonomy; I’ve been looking at
model-airplane two-stroke diesel glow engines and things like that,
since those seem to be the smallest heat engines around, but it’s hard
to find information about their efficiency, and they’re also messy and
noisy and don’t scale down to low power.  In Dercuano I concluded that
under ten milliwatts on average, during use, was adequate for a
responsive interactive computing experience, mostly to update the
screen.  12 hours a day of usage for a month at 10 mW works out to
13 kJ, which is only about 1.6 kg of aluminum, so maybe the aluminum
fuel cell can solve my problem.  It certainly seems to scale down
better than heat engines do.

It would probably be difficult and somewhat dangerous to get high
current output from such a battery, for all that aluminum is easily
available in 10-μm-thick foil.  I think you’d have to finely powder
it, with all the risk of class-D fires and possible waste via air
oxidation that that would entail.  But you could likely get it to
work.

Magnesium is another possible anode fuel for such a battery; GE
produced such a device in the 1960s, using the same NaCl electrolyte.
I think it does not have the problem of fouling the anode with a
sticky gelatinous hydroxide, but I think its specific energy is lower,
because a magnesium atom has but two electrons to give for its
battery; but the voltage is higher, which might compensate.
