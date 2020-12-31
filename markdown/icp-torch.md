An inductively coupled plasma torch could operate at atmospheric
pressure without consumables.  An initial seed plasma is provided by a
glow discharge, a short-lived conventional arc, or (if we permit
consumables) a conventional oxidation flame; then it is advected into
the center of an induction coil, where inductively-coupled power
transfer brings it to a high temperature and propagates it opposite
from the advection direction to sustain it in a constant position.  In
addition to conventional water cooling, the coil can be separated from
the plasma by low-permittivity, non-ferrimagnetic, insulating
refractory ceramics; for example, magnesia, lime, silicon nitride,
alumina, urania, thoria, or boron nitride; the ceramic itself might be
actively cooled as well.  (Refractories unusable due to high
conductivity include graphite, amorphous carbon, silicon carbide,
tantalum carbide, zirconia, and the diborides and nitrides of hafnium,
titanium, and zirconium; and silica is probably too low-melting,
although fused quartz does have an attractively low TCE.)

[The electrodeless plasma thruster article][2] suggests further
possible ways to initiate plasma formation, including electron guns
and laser ionization, and I suppose in theory a sufficiently powerful
ultrasound wave converging on a point ought to heat it enough to
produce plasma too, as in sonoluminescence, but doing that without a
liquid seems like it would be hard.

[2]: https://en.wikipedia.org/wiki/Electrodeless_plasma_thruster

The problem remains of how to limit the damage to the ceramic walls
from the plasma, since plasma-ceramic contact would surely ablate the
surface fairly rapidly; under uniform conditions the outer plasma will
tend to shield the inner plasma from receiving inductively-coupled
energy, so the natural tendency is for the plasma zone to grow.  Even
under adverse applied magnetic field conditions, by establishing a
gas-flow profile within the induction ring in which the flow near the
walls is much faster than the flow in the center, it should be
possible to adjust the induction power so that the plasma is
self-sustaining only in the center, while being blown away faster than
it can form around the outside.  (Of course, if the plasma were to
reach the ceramic wall it would also be self-sustaining there, since
in contact with the wall it would be stationary, but the plan is to
avoid this.)

It might be possible to manipulate the magnetic field conditions
instead of the gas flow conditions to keep the plasma away from the
walls, for example by making the induction coil smaller than, and
axially displaced from, the ceramic aperture.  I think this would
imply that the field would get stronger axially into the torch body,
creating a strong tendency for the plasma to propagate into
unprotected areas of the torch, but this could be countered by a
stronger negative advection divergence: all the gas closer to the
induction coil would be moving too quickly for the plasma to spread
into it.  I’m not sure if this is feasible.

In this scenario there is still radiative transfer of heat from the
plasma to the ceramic walls, but this can easily be kept low enough to
avoid wear to the ceramic.  If an arc between conventional graphite
electrodes is used to initially ignite the plasma, the electrodes will
erode somewhat, but if we’re talking about one spark every 20 minutes
of use or something like that, it should be easy to make the
electrodes big enough to last the life of the rest of the torch.

Such a plasma is of course easier to sustain in gases like argon or at
lower pressures, but air plasma has the great advantage of not
requiring any consumables, just an air compressor.

Probably the frequency required to efficiently couple into the plasma
would be on the order of a megahertz for human-hand-tool-sized
torches, hundreds of kilohertz for larger torches, and several
megahertz for smaller ones.

The torch might require active electronic control at submillisecond
timescales to stabilize the plasma and keep it from either blowing out
or flashing back.  Both the complex impedance of the induction coil
and the blackbody radiative flux from the hot plasma could provide
crucial feedback information.

Operating such a torch in a pulsed mode might be feasible and simplify
the process further: the induced current in the plasma tends to
Z-pinch it into a toroidal plasmoid while repelling it from the
induction coils, and hence from the torch.  [Sakharov reportedly took
this to the logical extreme][1] by vaporizing a small aluminum ring
with eddy currents into a self-contained plasmoid traveling at 100
km/s, powered by an EPFCG.

[1]: https://en.wikipedia.org/wiki/Andrei_Sakharov#Magneto-implosive_generators
