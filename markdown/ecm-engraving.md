[PEMTec claim][0] that with ECM they get surface reproducibility (I
think?) down to 30 nanometers.  This is an extremely promising figure
for micro-engraving of information or machinery on metal surfaces
using movable ECM electrodes.  They claim to use a process gap of some
microns, a salt-water electrolyte, “an exact current pulse”,
“workpieces with an imaging accuracy in the lower micrometer range”,
and oscillating die-sink tool electrodes, and get “a surface quality
of up to 0.03 micrometers.”

[0]: https://www.youtube.com/watch?v=9K9cZeO33rk "PEMTec corporate video EN 2018"

(Magnetic impulse engraving is also potentially interesting: a large
high-permittivity core brought down to a sharp needle point resting on
a copper or aluminum surface, with a high-current pancake-stack coil
wrapped around the core and connected through a step-down transformer
to a high-voltage source with a fast switch such as a spark gap.  This
ought to produce, I think, enough force from the eddy currents around
the sharp point to plastically indent the softer metal, but I haven’t
done the math to check this.  I guess I could do an experiment, but if
it fails, that would only provide evidence that a particular
configuration didn’t work, and I’d still have to do the math.)

ECM is also a potentially valuable technique for getting sharp metal
conical points, flat surfaces, cylindrical surfaces, and spherical
surfaces.  By rotating the workpiece past an ECM “form tool” electrode
you can do “ECM lathing”; if the form tool is a straight edge and also
translates parallel to that edge, then small errors in the edge will
be smoothed out, somewhat analogous to lapping — but permitting the
formation of precise *conical* and *hyperboloid* shapes (depending on
whether the edge intersects the axis) as well as cylindrical and flat.
For a spherical surface, you want to use a concave circular form tool
instead, and rotate it around its center of curvature.

A taut wire may be an adequate straight edge for many ECM purposes,
and a taut wire being moved back and forth may be an adequate plane.

For high precision, this is superior to traditional lathing because
the forces distorting and heating the workpiece and tool can be made
arbitrarily low.  Sometimes, though, it may be more desirable to
maintain a positive fluid pressure in the gap in order to control the
gap between the tool and workpiece more precisely than the position of
either can be controlled independently.  When this pressure can be
spread over a large area, it should produce no *local* distortion, for
example in the shape of the surface, only global distortion.  However,
in this case, only the cylindrical, flat, and spherical shapes
achievable by lapping are achievable, not the wider range of shapes
achievable on the traditional lathe.

(A different, widely-used electrochemical approach to sharpening is
isotropic electrochemical etching; by isotropically eroding the metal
by some distance *d*, any rounded features of radius less than *d*
should in theory shrink to a point.  This doesn’t produce precise
shapes but it does produce sharp points.)

Plasmas, especially nonthermal plasmas, may be better working fluids
for fluid-bearing purposes than traditional liquid electrolytes,
particularly if they contain groups such as carbonyl which form
low-boiling-point compounds with the workpiece metal.  They would
permit a much smaller process gap at a given pressure, and plasmas
containing oxygen, hydrogen, or fluorine should be able to erode
graphite, silicon, silicon carbide, and diamond, although in this case
we are perhaps going a bit afield of ECM proper.

With a tool electrode shaped like an air-hockey puck with a needle
stuck through it, the fluid-bearing technique will give extremely
precise control of the process gap.  To engrave precise
three-dimensional shapes you still need precise positional control of
the other two axes, though; while a kinematic mount consisting of six
such fluid bearings able to swivel would achieve this, we wouldn’t be
cutting inside those bearings, so traditional piezoelectric or
galvanometer approaches are probably better.

(Probably EDM is a better fit than ECM for “lathing” and “lapping”,
since its material removal rate is both higher and has a much sharper
falloff with distance from the workpiece, but ECM will make it
practical to do this with tungsten, copper, and tungsten-copper
alloys, and with plasma, even semiconductors such as graphite.)

This technique should make it possible to produce, among other things,
precise sharp-pointed electrodes for uses such as electrochemical
engraving and scanning-probe microscopy.
