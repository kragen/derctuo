At the Ohio State University, as in many other places, there is a
giant solid ball of granite floating in a pool of water.  This is a
surprising sight, since granite is not known for its buoyancy, but
it's real; you can spin this three-meter-diameter sphere around with
your hand and feel its massive weight slowly easing into motion in
precisely the way a giant granite boulder sitting on the ground does
not do.

This remarkable phenomenon is the manifestation of a fluid bearing,
like the air bearings commonly used in semiconductor-handling
equipment or an air-hockey table, but in this case the joint is a
ball-and-socket type.  Water is pumped up underneath the boulder,
lifting it just enough to allow the water to escape around its edge,
where without water it would rest on a circular stone "valve seat"
whose diameter is almost as large as that of the boulder --- exactly
like a ball-bearing-type check valve, with gravity instead of the
spring.  Only enough water pressure is needed to support the average
vertical thickness of the boulder, (2/3) τr³ / ½τr² = r/3, about half
a meter; at 2.4 g/cc and 9.8 m/s/s, that works out to about 12 kPa.
In theory the water flow rate at this pressure can be arbitrarily low;
and lower water flow rates give higher positioning precision, but also
reduce the "side loading" force needed to crash the boulder into the
valve seat, incurring static friction and potentially scratching it.
In practice the boulder is thus suspended using several liters per
second of water, which means that only on the order of 100 watts is
required to sustain this numinous apparition.

A really delightful attribute of fluid bearings of any kind is that
they have no static friction: as the velocity approaches zero, so do
the viscous losses in the fluid and thus the friction; thus the
boulder is always rotating.  So one of their key applications is for
low-velocity kinematic pairs.

This brings us to single-point incremental forming, in which you shape
a metal sheet by pushing a metal finger into it and moving it around.
SPIF, like 3-D printing, is capable of producing a wide variety of
different shapes with no per-shape tooling, but it can produce
fully-dense forged sheet-metal pieces with no material waste and no
postprocessing required, just like the more usual kinds of sheet-metal
presswork, sometimes approaching the capabilities of deep drawing.
The toolpath planning process is somewhat more involved than for 3-D
printing because you need to do a FEM analysis of candidate toolpaths
to anticipate when they would cause the metal to overheat, wrinkle,
tear, or get too thin.

In particular, friction with the forming tool is a major obstacle, and
can be unpredictable.  Typically the tool is a round shank of tungsten
carbide with a hemispherical end that is polished smooth, and to
reduce friction this tool is both lubricated with oil and rotated as
it moves around the work.

It occurs to me that the floating-boulder trick offers a far more
expedient alternative, if you shrink it down and crank up the
pressure.  Instead of the round end of a carbide shank, you use a
floating ball bearing --- ideally ceramic, but maybe just metal ---
and support it in a liquid bearing that presses it against the
workpiece.  This allows you to roll it around the workpiece like the
ball of a ballpoint pen rolls around on paper, entirely eliminating
static friction and greatly reducing dynamic friction.  (Ballpoint pen
balls do have static friction because the ink isn't pressurized, so
the analogy isn't perfect.)

The lubricant pressure needs to slightly exceed the average pressure
across the contact area between the tool and the workpiece, which
probably comes within about an order of magnitude of the yield stress
of the workpiece metal, perhaps tens of MPa (ASTM A36 structural steel
is supposed to have a yield stress of 250 MPa).

Lacking any experience with SPIF, my reasoning is as follows.  If your
tool diameter is small compared to the metal sheet's thickness, then
it won't be able to form the whole sheet; it will just make an
indentation into one side, which is not what we want.  Also, if the
tooltip is made of a similarly hard material, it will be deformed just
as much as the workpiece, which is very much not what we want.  If the
tooltip diameter is a few times larger than the workpiece's thickness,
then the pressure applied across the whole tooltip face sums up to a
tensile force that is resisted by a ring of workpiece material around
the outside of the tooltip, and perhaps only on some sides of it, and
this allows you to do SPIF as desired.  But if the tooltip diameter is
many times larger, you will be needlessly giving up surface precision,
and additionally you will have a greater tendency to just move the
workpiece around and rip it rather than forming it as intended.

So I tentatively conclude that you probably want the tooltip diameter
to be a few times larger than the workpiece thickness, but not two or
more orders of magnitude larger.  So, for example, if you are
indenting an 0.2-mm-thick sheet with a 3-mm ball, you might need to,
very roughly, overcome the yield strength in an 0.2-m annulus around
the 3-mm ball using the pressure across the 3-mm circle; this requires
about 1/14 of the yield stress to be present across the surface of the
ball, about 18 MPa (2600 psi in archaic units) in the case of 250-MPa
steel.  This is a feasible but challenging and somewhat hazardous
pressure for hydraulic fluids --- particularly when the lube is going
to be squirting every which way --- so where feasible it would be nice
to use a tooltip that is larger in comparison to the gauge of the
stock.

(Of course, in reality the pressure distribution is not uniform across
the face of the tooltip, nor is the tension distribution uniform
around the outside.)