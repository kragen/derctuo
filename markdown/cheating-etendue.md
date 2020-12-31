One way to cheat conservation of étendue is apparently to use a highly
diffusively reflective cavity with only a small pinhole in it to let
light out, but in practice I don’t think this works out.  A large
extended light source inside the cavity will tend to make the pinhole
as bright per unit area as the light source itself; if the light
source flashes only briefly, the light will escape through the pinhole
over a longer period of time than the original light source was
emitting.  In theory this ought to allow you to reduce the étendue of
the light source: the light is originally emitted over, say, 100
square millimeters and 4*π* steradians (isotropically), but then
escapes through, say, 0.01 square millimeters and 2*π* steradians.

For the moment, let’s suppose the cavity is perfectly reflective and a
perfect Lambertian diffuser; there’s no known way to achieve this, but
it doesn’t violate at least classical optics or classical
thermodynamics.  (There might be a quantum-physical reason it’s
impossible, but I don’t know of one.)

For straightforward thermodynamic reasons the brightness escaping
through the hole is the same as the brightness of the emitting
surface: the emitter has to have the same absorptivity and emittivity,
so when it turns on, the light level in the cavity rises until it’s
absorbing the same quantity of light that it’s emitting and therefore
is in thermodynamic equilibrium; at this point the entire reflective
cavity is reflecting light at the same illuminance: however many lux
(lm/m²) the surface of the emitter is, that’s how many lux the cavity
surface is too.  And the hole is the same brightness as the rest of
the surface.

So if you flash the emitter for 1 nanosecond, for example, then the
light will escape through the hole at the same brightness over a
longer period of time, maybe 10 μs: ten thousand times less étendue in
exchange for a light pulse ten thousand times longer.  Of course,
though, there’ll be a finite rise time and an exponential falloff to
zero, and depending on how the emitter works, it might extract energy
from the cavity by non-optical means.  (For example, if it’s an LED,
it would just heat up without emitting light, perhaps also driving a
little photocurrent given the opportunity.)

So, in effect, the light in the cavity behaves pretty similar to an
incandescent thermal mass, cooling off by emitting light through the
pinhole.  It’s almost effectively just a cavity absorber; we don’t
gain much from the perfect reflective surface, although the “cooling”
is proportional to the remaining energy, rather than the fourth power
of the remaining energy as in Stefan–Boltzmann cooling.  The emitter
rather quickly will become a real incandescent thermal mass, though,
and by hypothesis it’s much larger than the exit pinhole, so I think
even this apparent difference will turn out to be illusory: the
brightness in the cavity will follow the brightness of the emitter
much more quickly than it decays by escaping through the pinhole.

(You could argue that a Lambertian diffuser doesn’t conserve étendue,
and is thus cheating, but ⓐ you can do an adequate imitation of a
Lambertian diffuser with a pockmarked mirror, with lots and lots of
little reflective craters covering its surface, and such a mirror
*does* conserve étendue; ⓑ a Lambertian diffuser *increases* étendue,
while the cavity system described above seemed interesting because of
the possibility of *decreasing* étendue.)

There’s also the issue that in fact the most reflective known
materials for visible light are in fact only about 95% reflective, so
if the pinhole is less than 5% of the surface area of the cavity, the
system loses more energy to its walls than through the pinhole.  So
you really can’t get much benefit from this hack with known materials
anyway.
