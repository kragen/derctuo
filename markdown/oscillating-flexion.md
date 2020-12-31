A single perfectly rigid reciprocating rod transmits power only
intermittently; near the moment between its movement in one direction
and the movement in another, the power it transmits falls to zero,
unless it experience an infinite acceleration in that movement, and
thus too an infinite force, if its mass not be infinitesimal.  This
must happen twice per cycle.

Two such rods can transmit power continuously by virtue of being out
of phase.  If the resistance against which they push and pull be
variable, they can even transmit constant power; this is the case for
example if they are each in simple harmonic motion in quadrature and
connected to equal idealized dashpots; for the power transmitted by
one varies as *k* sin² *xt*, the other as *k* cos² *xt* = *k*(1 - sin²
*xt*).

A more interesting case is where two pushrods push and pull a
crankshaft rotating at a constant speed: a pushrod whose displacement
at time *t* is *y* sin *xt*, whose velocity is thus *xy* cos *xt*, has
a lever arm for its crank of *k* cos *xt*.  If its force should vary
in proportion, *z* cos *xt*, then the power it transmits will be *xyz*
cos² *xt*, just as in the dashpot case, and summing with a pushrod in
quadrature provides constant power transmission.

Now we can see also that the torque on the crankshaft from the first
pushrod is *kz* cos² *xt*, and so the pushrod in quadrature brings us
also to constant torque.

If we want to transmit power some distance, pushrods are impractical
because they will buckle unless supported.  If we use four cranks
instead of two, we can substitute four longitudinally oscillating
cables rather than two pushrods, transmitting power only through two
of the cables at any given moment, and clearly getting the same
constant power transmission.

This is temptingly analogous to two-phase AC power transmission,
strongly suggesting the possibility of improving efficiency by using
three cables oscillating at phase angles of 0°, 120°, and 240°, rather
than four quadrature angles.  The situation is not totally analogous;
if the tension on each cable varies in proportion to its velocity as
before, the power transmitted by a cable at phase angle *φ* is
ReLU(sin *xt* + *φ*)².  I think these still sum up to a constant, but
if not, varying the tension according to some different curve can
clearly provide constant power transmission over three cables.  I’m
not entirely sure that this would also provide constant torque, but
actually I don’t care.

That’s because the reason I think this is interesting is for
continuously transmitting power between parts of a flexure; flexagons
aside, most flexures can’t manage continuous rotation, so the
traditional forms of continuous mechanical power transmission such as
belts, shafts, and gears are unhelpful.  Cable transmission also has
the generally-noted property of fitting conveniently into smaller
spaces.

Such oscillating cable transmission might use radically non-sinusoidal
force and displacement profiles — for example, think of four cables,
of which at any given time three are under high tension transmitting
power, while the fourth is under low tension but higher velocity,
being returned to its starting position.  This sort of thing can, I
think, increase the material efficiency of such power transmission
systems.

A cable tensioned at 2 GPa traveling at 1 m/s is transmitting 2
gigawatts per square meter, which is 2 kilowatts per square
millimeter.  If the cable can only be safely tensioned to 200 MPa, it
can only transmit 200 W/mm² at this speed.  In theory you could
increase the cable speed arbitrarily — for example, your wimpy 200 MPa
cable would transmit 4 MW/mm² at 20 km/s — but, aside from concerns
about sonic booms and friction heating, the process of reversing the
cable’s direction of movement involves accelerating it, which also
requires force, and that force adds to the cable’s tension.

However, for cable lengths short compared to the free breaking length
of the cable material, this acceleration can reach many gees before
the load on a cable during the return stroke equals the load during
the power delivery stroke.  Indeed, the number of gees is precisely
the ratio of the cable length to the free breaking length.  So, for
example, gel-spun UHMWPE at 3 GPa and 0.96 g/cc has a free breaking
length of 319 km, so a 100-mm-long cable can be accelerated by pulling
on one end at about 31 million gees before it breaks.

Return strokes faster than some 10–100 times the acoustic length of
the cable will result in waves noticeably propagating back and forth
in the cable, unless it’s properly acoustically terminated to prevent
such reflections.  This is precisely analogous to the phenomenon in RF
electronics with unterminated transmission lines, but I don’t know of
any electrical power transmission scheme for which it is essential to
keep the mean drift velocity of the charge carriers in the cable to
zero, so the analogy only goes so far.

To reduce the total displacement of the cable and thus permit higher
instantaneous velocities and power densities, it might be desirable to
transmit power at much higher acoustic frequencies than this, such
that indeed many wavelengths of the tension wave fit within the cable.
Taking this step renders the power transmission capability of the
cable independent of its length.

As one point among many in this possible design space, consider four
parallel cables oscillating in simple sinusoidal motion in quadrature,
carved from ASTM A36 steel, each 100 μm square.  According to [Machine
Teeth](machine-teeth.md)
A36 yields at 250 MPa, and its Young’s modulus is 200 GPa.  It weighs
7.9 g/cc, so if they’re 200 mm long, each weighs about 16 mg.  Suppose
they’re oscillating longitudinally at 3 kHz by a distance of 100 μm.
Their peak speed is only 1.9 m/s, but their peak acceleration is
36 km/s/s, 3600 gees, which requires about 570 mN peak acceleration
force, working out to 57 MPa, comfortably below A36’s yield stress.
Such a stress will elongate the wire by 0.03%.  If the one or two
wires actively transmitting power at any given time are loaded
sinusoidally up to 125 MPa, the peak power transmitted on a wire is
2.4 watts, but about 1.1 W of that is “returned” during the return
stroke for the wire.  I think this means that the total net power is
consistently about 1.3 watts for the whole assemblage.

This is a promising but not outstanding amount of power to transmit
through something the thickness of a beard hair.  How can we increase
it?

If we increase the distance of displacement, holding constant the
frequency, then we linearly increase the velocity and thus the power,
at the expense of linearly increasing the acceleration.  If we
increase the frequency instead, while holding the displacement
constant, then we linearly increase the velocity but quadratically
increase the acceleration.

If we use a (nonexistent) material of the same physical
characteristics except that it had a lower density, or if we were
transmitting over a shorter distance, it would reduce the force
required to accelerate the wires; we could trade that for a
reciprocally higher acceleration.  If instead we used a material with
the same physical characteristics except a higher yield stress, such
as a harder steel, then we could proportionally increase both the
acceleration *and* the load force.

The totally free way to improve the system seems to be decrease the
frequency linearly while increasing the distance quadratically, thus
holding the acceleration constant while increasing the velocity and
thus the power reciprocally with the frequency.  So maybe we could
increase the total displacement to 10 mm while decreasing the
frequency to 300 Hz, increasing the power to about 13 watts, a much
more respectable power level.  Further development in this direction
would seem to be dependent on very precise motion control to avoid
having to space the wires further apart.

If we drop the density by a factor of 8 while multiplying the yield
stress by 10, then the first would allow us to increase the frequency
and power further by a factor of 2.8 (increasing the acceleration by
8), and the second would allow us to increase the frequency by a
factor of 3.2, but also the tension by a factor of 3.2, increasing the
power by a factor of 10.  I think this means you could transmit 360
watts of mechanical power through your new hair-thin gel-spun UHMWPE
cable, at least for 20 mm.

(In practice I doubt this could be sustained continuously; the
imperfectly elastic nature of any real material results in some heat
dissipation from stretching and relaxing it, and UHMWPE is, I suspect,
worse in this aspect than steels.  Moreover it melts at a very low
temperature; tens of milliwatts of such dissipation would likely be
fatal.)

With the acoustic traveling wave mode of energy transmission mentioned
earlier, the maximum power per unit area is the energy’s elastic
energy density (half its yield stress multiplied by its yield strain)
multiplied by the speed of sound in the substance.

Consider how the example above compared to electrical power
transmission through an electrical cable of comparable size.  If you
enclosed three 40 AWG solid copper wires, each 80 microns in diameter,
in 20 microns of [Kynar] insulation, you would have a similar-sized
cable. You could run three-phase AC power over it at whatever
frequency was convenient; your phase-to-phase voltage would be limited
by the breakdown voltage of 40 μm of Kynar, while your RMS current
would be limited by the resistance per meter and heat dissipation per
meter of the cable at Kynar’s maximum service temperature
of 149°.  You can
decrease the radius of the conductor and increase the thickness of the
Kynar by an equal amount, thus increasing the voltage linearly but
also increasing resistance as the square of the remaining wire.

[Kynar]: https://www.ipolymer.com/pdf/PVDF.pdf

The dielectric strength is supposedly “1700 V/mil” for a short period
of time; if we figure that’s 1000 V/milli-inch in practice, that’s
about 40 volts per micron, so 1600 volts peak, 1100 VAC RMS
phase-to-phase.  [40-gauge wire is rated for 90 milliamps over short
runs][wire].  I think this ends up at a few hundred watts, too, so
it’s the same ballpark.

[wire]: https://www.powerstream.com/Wire_Size.htm