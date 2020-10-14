How can you measure a wide range of temperatures without exotic
materials?  High-temperature thermocouples and thermistors typically
use things like platinum and iridium because they don't oxidize in
air.

A cartridge heater, as I understand it, consists of a resistive
heating element coiled up, packed into a solid insulator made of
something like MgO or BeO, combining very high resistivity and high
dielectric strength with high thermal conductivity; all packed into a
metal can to exclude oxygen and avoid abrasion of the relatively weak
insulator (very weak in the case of MgO).

You can get fairly fine (≈100μm) copper wire by, among other things,
untwisting Ethernet cables.  [Annealed copper's temperature
coefficient of resistivity α is about 0.0039][0], comparable to
platinum's.  You could maybe stick such a coil of fine copper wire
inside some kind of insulator, such as quartz sand, and do a four-wire
resistance measurement on it, using more copper wires twisted onto it.

[0]: https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity#Resistivity_and_conductivity_of_various_materials

Exploring a candidate design: millikelvins to 1000° should be attainable
------------------------------------------------------------------------

To be concrete, 200 mm of 100-μm-diameter annealed copper wire should
be about 439 mΩ at room temperature, about 610 mΩ at 120°, and about
2.15 Ω at 1020°, close to [copper's melting point of 1085°][1] (though
probably α diverges a bit by then).  If you were to dump a 1-amp pulse
through it through two of the wires for 100 μs, then it should develop
a voltage on the order of 400–2000 mV across the other two wires;
100 μs is plenty of time to measure the voltage and current waveforms
even with a 44.1 ksps ADC, much less a 1 Msps ADC.

[1]: https://en.wikipedia.org/wiki/Copper

What error should we expect?  Let's suppose we can calibrate out the
thickness of the thermistor, copper's nonlinear temperature-resistance
curve, and the impurities in the particular copper wire we're using.

An input impedance of 1 MΩ on whatever voltage measurement thing you
have connected to the other two wires would give you a parasitic
offset current on the order of 1 μA, causing a relative error of 10⁻⁶
or so on the temperature measurement.  An op-amp with lower offset
current would provide a more precise measurement.

The current you measure on the current wires outside the device would
differ from the current through the "thermistor" only due to parasitic
capacitances around the thermistor; if these were, say, 10 pF, then
the current error would be around 10 pA, a relative error of 10⁻¹¹.
For this error to rise to 10⁻⁶ you would need 1 μF, a totally
unreasonable amount of parasitic capacitance.

Parasitic inductance is larger, but it's less of a problem.  Suppose
you have 100 nH of parasitic inductance in each pair of leads, which
you can reduce by keeping them as closely antiparallel as possible,
and another 100 nH in the "thermistor" itself (which you could reduce
by alternating winding directions, FWIW).  This would be very large;
[axial-lead TVS diodes are around 10 nH][2] while surface-mount
devices are closer to 4 nH.  And suppose the current ramps up to 1 A
in 10 μs.  This produces 10 mV of voltage drop on the current leads
and 10 mV of inductive voltage in the thermistor superimposed on the
actual desired voltage.  The first doesn't matter; the second, without
any further measures, would produce a temperature error of some +6°
during that time.  Since the current through the voltage-sensing wires
is sub-microamp, the error from their inductance is a million times
smaller.

The reason this is less of a problem is, first, once the current
finishes ringing, for the rest of the 100-μs pulse, the inductance
introduces no error at all; second, you're probably applying more
voltage than that, so the ramp time is even faster; third, if you take
two or more samples of the voltage and current during the pulse, it's
easy to decompose the results into an inductive component and a
resistive component.

[2]: https://www.microsemi.com/document-portal/doc_download/14608-micronote-111-parasitic-lead-inductance-in-tvs

Heat is the biggest error.  1 amp through 439 mΩ is 439 mW, which is a
lot.  In 100 μs, it's only 43.9 μJ, which is not a lot.  But this is a
small wire; at copper's density of 8.96 g/cc, it's only 14 mg of wire.
Still, at copper's molar heat capacity of 24.440 J/mol/K, which
divided by its atomic weight of 63.546(3) g/mol gives 0.38460 J/g/K,
that's a temperature rise of 8.2 millikelvins, 14 μV.  Crudely, this
would introduce an error of about 3 × 10⁻⁵ in the measured voltage,
about 30× larger than the other sources of error discussed above.

However, this heating error is zero at the beginning of the
measurement process, and on short time scales it is proportional to
the integral of squared current so far.  (Over slightly longer time
scales the heat will start to leak away.)  So I think it's probably
relatively straightforward to cancel this source of error, at least
down to the same 10⁻⁶ level as the offset current.  If your ADC is
fast you might be able to just use a 3-μs current pulse.

The whole process of generating heat and conducting it away from the
wire into the rest of the device can be fairly closely approximated as
a linear time-invariant process, so we can estimate its impulse
response function, then cancel it, probably down to a 10⁻⁸ level,
particularly if you stimulate it with random current pulses rather
than regularly spaced ones.

It might be hard to get such a low error in the measurement of
voltages and currents, due to things like drift, noise, and the
temperature coefficients of the measurement device.  Still, it's
achievable, and even an 0.1% error in voltage would be an error of
1 K, which is enough for most of my purposes.

All of this is really quite astonishing, and it makes me wonder if I
am overlooking some kind of enormous source of error.

It's important that thermal expansion and contraction not change the
resistance of the current path, for example by tightening and
loosening connections.  For the voltage-measurement path, this is less
important.  So probably the right way to make the device is by tying
and/or soldering some voltage-measurement wires onto the relevant part
of the current-measurement wire.

Alternatives
------------

Carbon, being a semiconductor, has a temperature coefficient about an
order of magnitude higher, and it can also handle higher temperatures
than the 1085° of copper.  You can get carbon-composition and
carbon-film resistors already in sealed ceramic "cases", though
generally those aren't built to handle more than about 300°.  (The
paint on traditional axial-lead packages is organic and burns.  Maybe
surface-mount packages are better.)

Steel wire and stainless steel wire have temperature coefficients
similar to copper's, but can withstand higher temperatures without
melting, typically up to 1400° or more, though they must be protected
from oxygen at these temperatures.  They are better than copper for
use in hot-wire cutters (at temperatures low enough that they don't
burn) because they are stronger.  They're typically thicker, though;
stranded picture-hanging wire from an art store has the thinnest
stainless-steel wire commonly encountered.

Tungsten wire is also similar, and comes much thinner in conveniently
packaged quartz-halogen lightbulbs to protect it from oxygen, though
perhaps not all of those envelopes will themselves withstand
temperatures over 1000°.  (And these lightbulbs do double duty as
heating elements.)  However, they don't normally have four wires.

Silicon carbide, commonly used for heating elements and abrasives, is
also a semiconductor, and can withstand even higher temperatures
(decomposing at [2830°][3]), protecting itself against air up to about
1600° by oxidizing the surface to silica.  I don't know what its
temperature coefficient of resistance is, but I imagine that it's
negative and larger than copper's.

[3]: http://aries.ucsd.edu/LIB/PROPS/PANOS/sic.html

When using the same element to sense temperature and raise it, you can
either measure the average temperature to which the element is heated
(though not the peak, which is what you usually really want) by
measuring the voltage and current while it's on, or the temperature of
its surroundings by turning it off and letting it equilibrate.
