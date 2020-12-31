Geomagnetic energy harvesting is barely feasible at near-kilometer scales
=========================================================================

Variations in the geomagnetic field penetrate quite deeply into the
earth and sea and may be a feasible energy-harvesting source,
especially in the case of another Carrington event.

But how feasible is it?  The Halloween geomagnetic storm of 2003
provoked planetwide variations of some 10 μT, out of a total 25–65 μT,
with larger variations toward the poles, while [typical daily
variation is about
25 nT](https://en.wikipedia.org/wiki/Earth%27s_magnetic_field#Currents_in_the_ionosphere_and_magnetosphere),
"with variations over a few seconds of typically around 1 nT".

A rate of change of 1 nT (1 nWb/m²) per second is one nanovolt per
square meter inside a single-turn inductive loop; even if we have 1000
turns, that's still only a microvolt per square meter.  You could
imagine stepping that up to a usable 0.4 volts or so with a chain of
transformers; three 80:1 step-up transformers would probably serve.
If you were trying to get 1 μW, which is a challenging but achievable
level for modern energy-harvesting machinery to survive from, you'd
need 1 A m² at the 1000 turns level or 1000 A m² at the 1-turn level.

One problem is that you can't get an arbitrarily large amount of power
out of an inductor in a varying magnetic field just by winding more
turns around it; at some point the current that's being induced will
cancel out most of the magnetic field that would otherwise exist, and
you'll stop getting more power.

It turns out that the [energy of the magnetic
field](https://en.wikipedia.org/wiki/Magnetic_energy) is ½*B*²/*μ*, so
in empty space the difference between 30 nT and 31 nT is 24 pJ/m³, and
we probably can't capture more than half of that for
impedance-matching reasons, so we're probably limited to a few
picowatts per cubic meter.  (I don't think using higher-permeability
materials helps here; the *μ* is on the wrong side of that fraction.)

A further complicating factor is that, if you're using conventional
conductors, you probably need to use ridiculously thick wires.
Suppose your primary coil is 1000 m², the size of a big-box store
façade, and you're getting 100 μV out of 100 turns around it.
(Remember, unless you're near the poles, it has to be oriented
north-south.)  To get 1 μW you need 10 mA, and if you want no more
than 90% of the energy to be lost in heating the primary coil, you
need a voltage drop of no more than 90 μV, 900 μV per turn.  So your
130-meter-long coil needs to be no more than 90 mΩ per turn, which
requires 3-gauge copper wire (`~awg(2*~circlearea(130 m /
copperconductivity 90 milliohm))` in units(1) gives about 3.3), which
is is normally used for carrying 150 amps or more and costs abut US$3
per meter.

(I'm not entirely sure but I think you might need to enclose a larger
area to grab enough energy.  This helps a little with the wire
thickness because you can enclose a larger area per unit length of
wire, but the wire is still ridiculously thick.)
