A standard TL431 voltage reference is only accurate to ±0.5% at best,
more often ±2%.  TI's REF5050 is accurate to ±0.1% or ±0.05% in the
"high-grade" version, with 3 ppm/K temperature drift and 50 ppm/1000
hours, but those cost US$5–10.  The internal voltage reference in the
STMF103C8 used in many Blue Pill boards is specified as 1.16–1.26V
over the -40° to 105° range, an error of about ±4.2%, with a
temperature coefficient of 100 ppm/K.  The datasheet for the CKS32 in
the Blue Pill I got says the same thing in §5.3.4 内置的参照电压: its
V<sub>REFINT</sub> (内置参照电压) goes from 1.16V (最小值) to 1.26V
(最大值).

This is pretty shitty fucking accuracy.  4.2% is 42000 ppm.  For
measuring temperature with a tungsten wire near room temperature, a
±4.2% error is a ±10° error, and it gets larger at higher
temperatures.  Using a standard TL431 ripped out of some scrapped
power supply we can cut that error to 19000 ppm or so, but that's
still nothing to write home about.  How the fuck are you supposed to
build a fucking voltmeter?

Like, something similar to the open-source [Transistortester AVR][4]
by Kübbeler and Frejek, now commonly known as the "[M328 Transistor
Tester][5]" or "LCR TC1 ESR meter", which sells for US$38 here in
Argentina or US$13 in the US.  But with better accuracy, precision,
and repeatability, and ideally without any bought components.

[4]: https://www.mikrocontroller.net/topic/248078
[5]: https://www.instructables.com/AVR-Transistor-Tester/

The 0.1%-precision [LM4040CIM3X-20-NOPB][3] voltage reference costs
US$1.67 from Mouser.

[3]: https://www.mouser.ca/ProductDetail/Texas-Instruments/LM4040CIM3X-20-NOPB?qs=2k4gZbgf%2F9nz6KzcCN74VQ%3D%3D

> Some random YouChube video from TheSignalPath (#175 (ⅱ)) found
about 25 ppm of voltage difference between his not-recently-calibrated
Fluke 744 Documenting Process Calibrator and his
not-recently-calibrated 8-digit Keithley DMM 7510, so tens of ppm is
achievable in ordinary electronics labs, but expensive; he apologized
for the lack of calibration, so apparently he was expecting better.
His resistance error was worse, 4600 ppm at 100Ω, 400 ppm at 1kΩ,
20 ppm at 10kΩ; this pattern makes me suspect that this error was due
to not using a 4-wire measurement.  His current error was about
250 ppm.  Testing the 744 against its own meters he instead got
400 ppm emf error and 500 ppm resistance error; he neglected to thus
test its current measurement capability.

I tested a shitty digital multimeter from 2016, US$6 from the hardware
store, against another similar meter to get an idea of how bad they
are, and also how aggressive they are.  It seems like the old
multimeter's diode-testing range uses up to 1.4 mA at up to 2.72
volts.  Their diode test readings differ by about 2%, resistance by
about .03%, voltage by about .3%.  pretty impressive metrology for
US$6.  These are lower bounds on their actual errors, but they're
unlikely to be conservative by more than an order of magnitude or so.

By contrast, the quartz crystal on the Blue Pill is 8 MHz with,
probably, an error of about 10 ppm, common for watch crystals.  The
damn chip can measure voltages to 1.5 digits of accuracy and time to 5
digits of accuracy, 4200 times better.  (And it can count electrical
pulses to a lot more accuracy than that, to the point that the concept
of tolerance stops being meaningful.)  We can get down to probably
1 ppm timing error if we can measure the temperature to compensate,
0.1 ppm if we can control it.  (And nowadays we can reference to GPS.)

*Ratiometric* voltage measurements with the dual ADCs ought to be a
lot more precise, and probably limited only by the 12-bit bit depth
and whatever the noise is.  So, for example, if we run an exactly
known *current* through a tungsten lightbulb and measure the voltage,
we're subject to that shitty ±4.2% precision; but if we put some
*unknown* voltage across a series combination of the lightbulb and an
exactly known *resistance*, we can get a much higher-precision
measurement.

Every time we quadruple the measurement time, we add another bit of
precision, because the variances of Gaussian noise combine additively.
So the sum of four measurements has four times the variance, thus
twice the standard deviation, as a single measurement, and their
average thus has half the standard deviation.  The ADCs run at 1Msps,
so if we take 1048576 samples, 1.05 seconds' worth, we can get a
22-bit-precision reading, which has a quantization error of ±0.125
ppm.  Probably averaging 65536 or 32768 samples is a better tradeoff,
giving you more like 1 ppm error in exchange for much faster data
acquisition.

How do we get an even approximately known resistance, though?  Typical
resistors used to be ±20%, now they're ±1%, but nothing close to
1 ppm.  On MercadoLibre we can buy a temperature standard resistance
made of Weston's manganin, intended for converting precise voltage
measurements to current measurements or vice versa, for US$20.  At 25°
manganin's temperature coefficient of resistance crosses zero, having
fallen from 6 ppm/K at 12° on its way to -42 ppm/K at 100° and -52
ppm/K at 250°, before crossing zero again at 475°.  This suggests that
a ±10° error of temperature measurement around 25° would give you
about a ±5 ppm error of resistance.  However, these resistors are not
so precisely made, having a ±0.5% error, 5000 ppm; one wonders if they
are correctly annealed or even truly made of manganin.  Better
precision calibration resistors are available.

A better option might be a known capacitance; you can easily measure
the RC time constant as long as your voltage reference isn't too noisy
over the short time involved.  *C* = *εA*/*d*, so it's straightforward
to construct a capacitor with a known capacitance; however, if we want
its capacitance to be known to within, say, 10 ppm, we need less than
10 ppm error on all of *ε*, *A*, and *d*.  *A* is relatively easy: a 1
m x 1 m square of foil is a square meter to within 10 ppm if its width
and height are each accurate to within 7 ppm (i.e., an average of
7 μm) and it's square to within cos⁻¹(1-10ppm), which is about 15
minutes of arc.  Getting *d* accurate to within 10 ppm is more
difficult; if the dielectric is intended to be 100 microns, its
average thickness must be correct to within 1 nm.  But getting *ε*
accurate to within 10 ppm is feasible in only one way: vacuum.  Even
the permittivity of air is 590 ppm higher than vacuum, and it varies
according to pressure and temperature.  Common solid dielectrics are
hopeless; [WP gives][0] polypropylene's relative permittivity as
"2.2–2.36", i.e. ±35000 ppm.  A vacuum capacitor of these dimensions
would have a capacitance of 88.5419 nF.

[0]: https://en.wikipedia.org/wiki/Relative_permittivity

In despair, I turned to a shitty scan of NBS Monograph 84, from
1965-12-15, titled, "Standard Cells: Their Construction, Maintenance,
and Characteristics", by one Walter J. Hamer.  He explains to some
extent the construction of the Daniell cell, the Clark cell, and the
Weston cell, which served as laboratory references from 1836 until
1990.  A Daniell cell, using copper, zinc, and their sulfates, is
relatively straightforward to construct, but drifts rapidly over time
owing to the mixing of the sulfates, depends strongly on the purity of
the copper and the zinc, and has a horrific temperature coefficient.
The Weston cell was the first to have a roughly zero temperature
coefficient, which it achieves by the use of cadmium salts, which
unfortunately are rather difficult for me to procure.

Hamer also goes into the history of other metrological standards for
voltage (a word he disdains to use, preferring "emf") and other
electromagnetic units.  He points out that in theory you can use an
absolute electrometer as a standard, using the permittivity of free
space and precise measures of current, but that this gives an error on
the order of 100 ppm.  (He doesn't go into detail, but I think this is
a matter of charging a known capacitance, constructed as above, with a
known current and measuring the resulting voltage change.  Presumably
you could also use an electrostatic balance where the electrostatic
repulsion between two plates is countered by a sufficiently precise
weight to return them to their original position.)

As a standard of resistance, he describes the construction of an
air-core inductor of known dimensions and thus computable inductance,
and the measurement of its E–I relationship at different frequencies
to obtain a precise standard for the ohm; but he describes the Wenner
method, which seems to be some kind of differential measurement that I
don't fully understand, getting a ±5 ppm precision.  He also mentions
using the rotation of a magnet in a coil, or a coil in the earth's
magnetic field.  (The quantized Hall effect is the modern absolute
standard since 1990.)

He says that using computable capacitors to check the ohm measure is
"less involved" and "may be used [text lost] an annual basis" to check
resistance standards against absolute units in preference to the
inductive approach.  (He also says they normally used 1-pF computable
capacitors rather than the 88000-pF jobby I used above for
calculations.)  "Thompson-Lampard theorem" seems to be the key term
here.

Given the possibility of measuring a computable inductor or computable
capacitor with a microcontroller, I'd think that it would be easier to
do the measurements in the time domain rather than the frequency
domain.

Note that a precise standard of resistance, or a precise standard of
both time and either inductance or capacitance, allows you immediately
to convert between precise current and precise voltage.  Time is, as I
said, easy enough now.

Hamer then explains that an absolute standard of current is available
in the form of a current balance, in which you measure the
electromagnetic repulsion or attraction between two conductors against
a standard weight, and of course this is the standard definition of
the ampere; he says this is about ±6 ppm.  The method he describes
requires some kind of measurement over time, but I think current
balances are more commonly used in a quasistatic fashion, in which
after adding a mass, the current is increased until repulsion returns
the mass to its original position, thus requiring only measurement of
length and mass to calibrate.  And I think that in fact they are
commonly used nowadays for measuring masses in terms of known currents
rather than vice versa.

Force balances have the stunning metrological advantage that the
difference in force produces an acceleration, the acceleration
integrated over time produces a velocity, and the velocity integrated
over time produces a displacement.  So even a very small force
imbalance can produce an easily visible displacement, particularly if
you use it to deflect a mirror reflecting a laser pointer across the
room.

It's feasible, though not an everyday occurrence, to get weights that
are calibrated to within 1 ppm, though care must be taken to
compensate for local gravitational fields and atmospheric pressure and
humidity.  Atmospheric pressure can diminish by as much as 14% in
hurricanes, and of course varies by much more when you go up in
elevation or underwater; a 100 g steel weight that occupies 12.6582 mℓ
under some conditions is displacing about 15 mg of air, diminishing
its apparent weight by 150 ppm.  This can decrease by 2.5% from
humidity or 14% from weather-related pressure variation, causing a
deviation of some 20 ppm.  Thermal expansion and contraction of the
weights is not a concern; the 36 ppm/K volumetric expansion
coefficient of steel means that the above-described weight will occupy
12.6628 mℓ at a temperature 10° higher, displacing about 5 μg more of
air, lowering its apparent weight by 5 μg or 0.05 ppm.

Joe blocks, similarly, can measure lengths on the order of 100 mm down
to a precision of 0.1 μm or so, though they expand and contract by
12 ppm/K, thus requiring 80 mK control to reach 1 ppm.

The Clark cell, Hamer says, was adopted in 1893 as 1.434 volts at 15°,
though [the modern value is 1.4328][1], and its temperature
coefficient is -1.15mV/K.  It uses a zinc or zinc amalgam anode, a
mercury cathode, and a saturated aqueous electrolyte of zinc sulfate,
plus mercurous sulfate paste as a depolarizer, to prevent hydrogen
buildup on the plate (converting it instead into oil of vitriol;
removing impurities in the mercurous sulfate lowered the voltage by
another 300 μV).  This would probably also be challenging for me to
fabricate.  We can estimate that a ±10° temperature error would result
in a ±11.5mV error, about ±8000 ppm, which is why the Weston cell was
adopted as the new standard in 1908.

[1]: https://en.wikipedia.org/wiki/Clark_cell

The Weston cell has a temperature coefficient of about 41 ppm/K,
substantially better than the 800 ppm/K given above for the Clark
cell.

We can see why the NBS controlled the temperature of its Weston-cell
room to within 1°, the temperature of its oil baths for the Weston
cells to within 10 mK, and the oil baths' temperature during
measurements to within 1 mK.  Such measures applied even to the Clark
cell would have reduced its temperature-induced voltage error to some
0.8 ppm, at which point other errors would surely dominate.

It occurs to me that power might be a useful way to calibrate voltage
given a known current or especially a known resistance, because if you
can measure the power of a known resistance to within 1000 ppm, you
know the voltage to within 1 ppm; moreover, Δ*T* = ∫*P* d*t* /
*C*<sub>th</sub>, so you get the same kind of integration effect as
with force balances, but only to the first order, not the second
order.  Somewhat surprisingly, although specific heats vary with
temperature, some are in fact known to the required precision; for
example, according to definitions.units, the energy to raise a gram of
water from 14.5° to 15.5° is 4.18580 J, the energy to raise it from
19.5° to 20.5° is 4.18190, and the energy to raise it from 0° to 100°
averages 4.19002 per degree.  So you could imagine, for example,
heating 1000 kg (±1 kg) of very-well-insulated water by applying an
unknown voltage to a known resistance (±1 ppm) from 0° to 100° (each
±10 mK) and measuring the time required (±1000 ppm), and thus getting
a measurement of power to a precision of 1000 ppm, and thus measuring
the voltage to 1 ppm.  The imprecision of the specific-heat number
only adds about 1.3 ppm to the *power* imprecision, and thus 1.7 parts
per trillion to the voltage imprecision.

XXX hmm, maybe I screwed up that resistance calculation too?

XXX all of this stuff about calculating from power is based on a wrong
logic step.  1000 ppm power error gives you 500 ppm voltage error
(499.9 to be exact), not 1 ppm.

The main sources of imprecision in such an experiment would seem to be
the insulation of the water, which would have to leak less than 0.4
joules during the experiment, and the original measurement of the
resistance.

Which brings us back to the resistance-measurement problem.  It occurs
to me that it a computable *inductor* might be a more precise way to
measure a resistance, particularly if it can be made very small in
physical dimensions so that its magnetic dipole does not impinge on
materials with a substantial permeability; [wood's, for example, is
0.43 ppm][2] higher than the vacuum, a situation three orders of
magnitude more promising than the corresponding situation with
capacitors, and teflon's is even closer (how much closer is not
known).

[2]: https://en.wikipedia.org/wiki/Magnetic_permeability

A different tack might be to use some kind of differential measurement
to precisely calibrate the dielectric constant or permeability of some
material whose presence cannot be avoided but whose quantity can be
varied.  Operating the same air-gap capacitor at various air
pressures, for example, might enable you to extrapolate its
capacitance at hard vacuum, without having to actually produce 100-mPa
vacuums.

All these "sources of error" can equally well be seen as "observable
variables".  The only difficulty is untangling them.  If your
capacitor's resonant frequency (with a given coil) varies linearly
with the air pressure, then by measuring that resonant frequency with
1 ppm accuracy, you can measure the air pressure with 1-ppm accuracy.
If it also varies dramatically with the air's moisture content, well,
congratulations, you have [a moisture sensor](pet-dielectric-spectroscopy.md)
too, as long as you have
some other way to sense air pressure that varies differently with
humidity.  (Inversely, ideally, or failing that, not at all.)
Everything varies with temperature, but if some things vary more than
others and you can keep it at the same temperature, you can measure
the temperature.  If you find something that varies a *lot* with
temperature, maybe like the leakage current in a Schottky, you have a
very precise temperature sensor, which you can use to cancel out
temperature effects on other things, as long as you can characterize
them.  Force of gravity makes your watch crystal run faster when it's
sideways?  Great, kid, you gotcherself a MEMS accelerometer that costs
25¢.

