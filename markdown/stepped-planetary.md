I recently saw an amusing YouTube video of something called an
“orbital drive”, by “Skyentific”; it’s a sort of differential
planetary pulley without a ring gear, where a motor spins a planet
cage around two sun gears of different sizes, which are connected to
the planet idlers with belts.  The sun gears are planar, coaxial, and
in parallel planes, while the planet gears span both planes.  One sun
pulley is held fixed, while the other is free to rotate, one tooth per
cage revolution if the two suns differ by one tooth (and the planets
don’t change tooth count between sun planes).  It’s claimed to be
backlash-free (because it uses pulleys, I suppose) and of course
because it is differential it has a high reduction ratio, in the
neighborhood of 100:1.

The Wikipedia article on epicyclic gearing points out that, if you use
gears instead of pulleys, you can use two rings instead of two suns,
both simplifying hooking up the assembly and reducing its size, though
perhaps at the cost of requiring the planets to change size between
the sun planes.  (“During World War II, a special variation of
epicyclic gearing was developed for portable radar gear...”)

It occurred to me that if you use only a single planet, it can perhaps
be quite large compared to the ring gears, and you can cut a third
ring gear into the center of it which you drive with a small pinion,
thus gaining a further reduction without increasing the size of the
assembly.  Because this pinion and ring are subject to much smaller
forces than the other gear teeth, they can be much thinner.

To be concrete, consider the case where the ring gears have 103 and
106 teeth, the two steps on the planet have 69 and 71 teeth, the inner
ring on the planet has 50 teeth, and the pinion that drives it has 7
teeth.  (Using involute teeth the depthing cannot be correct for both
the 69:103 mesh and the 71:106 mesh, but the difference is about
0.014%, so it’s tolerable.  Hmm, can you even use involute teeth on a
ring gear?)  Let’s consider one revolution of the 106-tooth ring in
the rotating frame of reference of the planet “cage”.  The 106-tooth
ring and the 71-tooth planet each rotate 106 teeth.  The 69-tooth
planet rotates 106*69/71 = 103.014 teeth.

Wow, I didn’t expect THAT.  Is that real?  Hmm, consider one rotation
of the planet: 71 teeth on one step, 69 on the other, resulting in
71/106 rotation on the 106-tooth gear and 69/103 rotation on the
103-tooth gear, about 0.009% of a rotation difference between them.
Ratio this up via brute force: 106 rotations of the 71-tooth gear, for
a total of 7526 teeth of rotation in that plane, rotates the 106-tooth
ring 71 times; the same 106 rotations of the 69-tooth gear are 7314
teeth, which work out to 71.0097 rotations of the 103-tooth gear.
Let’s consider 103 times that: 10918 rotations of the 71-tooth gear
are 775178 teeth, 7313 rotations of the 106-tooth gear and 10918
rotations of the 71-tooth gear.  Those same 10918 rotations of the
69-tooth gear give us 753342 teeth of rotation in its plane, driving
the 103-tooth gear through 7314 rotations.  Seems legit: a 10918:1
reduction in the differential rotation!  So let’s continue.

But this seems impossible; you would think that the planet would have
to return to its initial position after 106 rotations.  Like, if you
mark the most-meshing tooth on it at the beginning, and also mark the
corresponding space between teeth on the 106-tooth ring, then after
106 rotations you would think it would have to come back to rest in
exactly the same marked place on the ring, which means that the
69-tooth planet is also in exactly the same place relative to the
106-tooth ring, since it’s rigidly fixed to the 71-tooth planet.  So
how could the 103-tooth ring be displaced by a fractional tooth?

Now, each revolution of this planet is 50 teeth on its inner ring,
which is 50/7 rotations of the pinion, coaxial to the outer rings,
that drives it.  This provides a further reduction of 7:50, for a
total of 7:545900, or about 1:77985.7.

But that’s in the frame of reference of the planet cage.  Let’s switch
to the frame of reference of the 106-tooth ring gear and let the
planet cage spin.  Every time the planet rotates through 106 teeth
(and 106/71 rotations) in its own frame of reference, the planet
carrier rotates by one revolution in this frame of reference.  Its
inner ring rotates by 50\*106/71 teeth in its own frame of reference;
from this we must subtract the single rotation of the frame of
reference itself, so 50\*(-1 + 106/71) teeth, which works out to about
24.64789 teeth.  This is about 3.52113 rotations of the pinion.

Remember that, if the dubious and probably wrong calculations above
are correct, the reduction is only 7314:1 from the perspective of the
106-tooth ring — that is, every time the 106-tooth ring rotates 7314
times in the frame of reference of the carrier, the 103-tooth gear
rotates 7313 times.  So this is the appropriate multiplier for the
pinion relative to the 106-tooth ring: every time the pinion rotates
3.52113 times, the planet cage rotates once, and the 103-tooth gear
rotates 1/7314 of a revolution, for a total reduction factor of only
about 1:25753.5.

Do the geometries work out?  Suppose we use a tooth module of 2 mm for
the outer rings and the planet teeth that engage them and 1.5 mm for
the inner ring and pinion.  Then our circumferences are respectively
212 mm and 206 mm for the outer rings and 142 mm and 138 mm for the
inner rings, so the diameters of the pitch circles are 67.4817 mm,
65.5718 mm, 45.2000 mm, and 43.9268 mm, and their radii are 33.7408
mm, 32.7859 mm, 22.6000 mm, and 21.9634 mm.  So the center of the
71-tooth planet would ideally be at 11.1408 mm from the center of the
106-tooth ring, and the center of the 69-tooth planet at 10.8225 mm
from the center of the 103-tooth ring, a difference of 318.3 microns.
This is probably sufficient for reliable meshing, but will definitely
introduce an undesirable amount of backlash, and only one tooth will
be in contact at a time on the 69:103 plane.  If this is unacceptable,
it might be feasible to have the 103-tooth ring revolve in a circle
with that 318-micron radius, although that would be a lot more
reasonable if the difference were in the opposite direction.

Then the inner ring’s pitch circle is 75 mm in circumference, and the
inner pinion’s 10.5 mm, giving pitch circle radii of 11.9366 mm and
1.67113 mm respectively.  This means that the pinion center will be
10.2655 mm from the planet and inner ring center, which is almost a
full millimeter off the desired outer ring center, which is 11.14 or
10.82 mm from the planet center, as calculated above.  This can be
fixed easily enough by using a slightly larger module (this gear with
a ring gear cut into its inner surface will not be a standard part
anyway) or slightly more teeth on the inner ring.  With [lantern
gears](lantern-gears.md), where the teeth of the inner ring are round
dowels rather than normal teeth (feasible since they can be anchored
laterally to the bottom of the 69-tooth planet layer) pinions with as
few as three teeth are feasible.

To keep the edges of spur-gear teeth in different layers from rubbing
on one another, it may be desirable to separate the two planes, either
with a mere spacer or with a solid circle that extends out past the
teeth of either planet.  If the circle is large enough, it could
extend out past the teeth of the ring gear as well, preventing any
edge-on-edge contact.

The same differential principle can be applied to get larger
reductions from the original “orbital drive” without sacrificing the
use of toothed pulleys: by increasing and decreasing the planet sizes
nearly in proportion with their respective suns, we can achieve very
large reductions indeed, far less than the one or two teeth per
revolution delivered by harmonic drive (strain-wave drive) or
cycloidal reducers.

The large unbalanced mass of the single planet may be a problem, as it
is in cycloidal drives, where it is conventionally balanced by a
second cycloidal drive in half-phase with the first.  However, the
initial 1:3.52 reduction from the pinion reduces this problem; the
angular velocity of the planet is 3.52 times lower than it would be
for a cycloidal drive driven at the same speed, and its acceleration
is thus some 12 times lower than it would be if you drove the planet
cage directly.  However, the planet’s center of mass is considerably
further from the center of the ring than the thing that moves around
in a cycloidal-drive system, compensating somewhat for this advantage.