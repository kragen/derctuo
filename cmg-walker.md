A control moment gyroscope, CMG, or гиродин is a device used, typically in
pairs, to control the attitudes of large spacecraft, as an alternative
to reaction wheels.  Someone has built a cute little cube-shaped robot
that can get up and walk by rolling from one corner to another using
reaction wheels, so the robot has no externally protruding components
or pivoting joints, other than the bearings for the reaction wheels
themselves.  It would be interesting to do something similar using
CMGs.

The idea is that you'd have something like an opaque, matte
tetrahedron with rounded corners, or perhaps some more irregular
shape, of a few hundred mm in diameter, mostly made of some very light
material; however, inside of it would be hidden two or more gimbaled
gyroscopes, whose rims would contain most of the mass of the whole
device, as well as several motors, a battery, and control electronics.
If the walker decides to start walking, it spins up its CMGs and
starts torquing them in order to stand up on, for example, one corner,
and move around.

As a concrete example, perhaps the height of the tetrahedron would be
720 mm, but the last 100 mm of the point are rounded off.  The
inscribed sphere has diameter 360 mm, so perhaps the largest gyroscope
has diameter 350 mm and can rotate to any angle within; the rim of its
perfectly toroidal rotor is, say, 100 mm in minor diameter, with a
circular cross section centered at 170 mm from the center, thus 340 mm
major diameter.  The torus has a volume of about 8.4 liters, so if it
is mostly or entirely made of lead, 11.34 g/cc, then it will weigh
about 95 kg, too heavy for most humans to lift.  I think it should be
possible to build the rest of the machine — frame, bearings, smaller
gyros, gimbals, motors, cables, etc. — under 5 kg.  So 95% of the
machine's mass will be in its primary gyro, which can safely be spun
at some 30 m/s.

At this speed its kinetic energy would be some 43 kJ, enough to drain
a 2400-milliamp-hour USB power pack just to spin it up, on the order
of 50 g of Li-ion battery.  (The 10050-mAh USB power pack next to me
weighs 205 g.)  So probably 1 kg or more would need to be battery.

So clearly this beast could have an angular momentum to be reckoned
with, and with the appropriate gearing, motors, and secondary CMGs,
would have no trouble at all slowly lifting itself off the floor to
stand on one point, or walking across the floor on two of its points.
It could perhaps walk up and down stairs, light up, vibrate, make
sounds, and, by balancing on one point, serve as a cocktail-party
coffee table, though keeping it from being a very noisy and
vibration-heavy table would take substantial engineering of the
bearings.

In addition to walking, it could tilt a bit to one side and rotate on
its rounded point, which would cause it to roll across the floor
rather than merely walking.

Equipped with a sense of touch to feel things placed on top of it, it
could balance a ball on its center, constantly tilting slightly to
nudge the ball back toward its center.  It might even be able to
simultaneously engage in such a motion while balancing an object on
its top.

If you wanted it to carry things around, though, a more useful
polyhedral shape would have an edge between two vertices, usable for
walking, opposite a flat face, so that it could walk while objects
remained on its upper surface mostly by friction, minimally tilting
back and forth to shift its weight between these two feet.  An
equilateral triangular prism, for example, would work; so, too, would
a square pyramid, though there is only one angle for such a pyramid at
which one of its triangular faces will be horizontal when its center
of gravity is over the opposite edge.

More irregular shapes would offer more versatility.

A prototype of 1% the mass could probably be constructed.  Instead of
weighing 100 kg, it would weigh about 1 kg.  You'd scale it down by a
linear factor of, say, 0.22.  So the tetrahedron would be 158 mm tall,
the incribed sphere 79 mm diameter, the rotor rim 22 mm thick,
centered 37 mm from the center (74 mm major diameter).  This rim has a
volume of 89 ml, 1.01 kg.  If we also scale down the rotor linear
speed and leave its angular speed alone, it's only going 6.6 m/s,
which is still 1700 rpm.  (I guess I should work out what the scaling
laws for CMGs are; I think that small CMGs are worse than reaction
wheels.)  The kinetic energy has dropped even more: now
it's only 22 J.  I feel like this would still probably work but you
might need to spin up the motors.

The total mass left over, if it scaled the same way, would be about
50 g.