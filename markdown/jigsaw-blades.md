Jigsaw blades break a lot.  In a sense that’s because the stroke of
the saw is greater than the elastic limit of the sawblade material.
But this is entirely avoidable.

If the stroke of the saw is *too* short, it won’t cut, because all of
the motion will be taken up by the elastic deformation of the sawblade
and the workpiece.  This is how saws for removing plaster casts avoid
cutting skin: their stroke length is shorter than the skin’s elastic
limit.  But the saw blade is typically much, much longer than the
distance the chips from the workpiece have to move to detach from the
workpiece; typical numbers might be 100 mm and 100 μm.  The [elastic
limit of a hard steel might be ½%][0] permitting about a 250-μm stroke
without risk of breaking the blade, which is plenty to cut the
workpiece; if this is not long enough, the jigsaw can be built bigger.

[0]: https://www.hitachi-metals.co.jp/e/products/auto/ml/pdf/yss_tool_steels_d.pdf "Hitachi’s brochure on their YSS die steels lists various steels as having 0.9–1.1 GPa yield stress and 200–230 GPa Young’s modulus, giving about 0.5% elongation at yield.  One especially strong steel had the same modulus but twice the strength and thus elongation."

It’s also necessary for the stroke length to be larger than the tooth
size, or each tooth will cut a separate hole, rather than joining the
holes together into a slot.  A higher movement frequency can be used
with smaller teeth and the same total material removal rate; moreover
thinner teeth and the elimination of the breakage risk sometimes
permit using thinner blades and thus lower total power; but sometimes
this undesirably reduces the achievable kerf curvature.

A typical electric jigsaw blade might move 1 m/s at 50 Hz.  The speed
of sound in steel is about 4 km/s, so a 100-mm-long stretched-tight
steel jigsaw blade will move more or less as a rigid body at
frequencies below about 40 kHz.  Moving 250 μm twice per cycle at
40 kHz would be 20 m/s, so such an ultrasonic jigsaw could probably
cut at a higher speed than a regular jigsaw without risk of breaking
the blade, at least if there’s some way to clear the chips.  If the
blade is 100 μm square, like one of my beard hairs or these hair-fine
copper wires I’ve been trying to solder with, and has an extra 50% of
non-tensile-load-bearing mass of teeth on one side, it weighs
120 μg/mm and so has a total mass of some 12 mg.  Accelerating it by
40 m/s in half of a 40 kHz would require 3.2 Mm/s/s, or 330 thousand
gees of acceleration, which works out to almost 40 newtons with this
mass, thus a stress of 40 MPa at the pulling end, about 4% of the
strength of steel.

This kind of sounds like an ultrasonic cheesecutter that can cut
through brass, mild steel, glass, bone, fingernails, hard plastics,
fired clay, concrete, and maybe wood and granite, but not actual
cheese as such, or your skin, or turkey.

Other possibilities to alleviate these compromises include using a
blade with omnidirectional teeth (for example a single helical tooth,
like a buttress-thread screw), which has no minimum kerf curvature
radius and can also be rotated between cutting strokes; mounting hard
teeth (whether high-speed steel or something like tungsten carbide) on
a softer blade that can stretch further; and force feedback through
electronics that stop pulling on the blade when overload is detected;
or coupling the saw frame to the jigsaw blade through a lightweight
spring that limits the force over the saw’s normal stroke.  But I’m
kind of excited about this ultrasonic cheesecutter thing.

To actually make it work you probably need synchronized but
mechanically weakly coupled pullers at the two ends of the sawblade,
like a two-man sawing team, rather than hoping the saw frame will move
as a sufficiently solid body.  By controlling the amount of slack with
some sort of feedback, they ought to be able to keep the tension on
the blade relatively constant.  Strain gauges in a lightweight saw
frame occur to me as one possibility.

The mechanical power going *into* the wire is about 20 m/s · 40 N =
800 W, but almost all of that is being transmitted from one sawblade
puller to the other over the wire, then returned 25 μs later; only a
small amount of it goes into the workpiece being cut.  You’d still
probably have to water-cool it.

An interesting feature of this device is that, because it runs at
40 kHz, its cutting action should be uncannily almost silent.

A vibrating engraver or scraper that works at scales, frequencies, and
powers like this, rather than the usual 50 Hz or so, would also be
very interesting.  It could push its hardened tip into the workpiece
as per normal.
