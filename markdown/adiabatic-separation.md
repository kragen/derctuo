Some novel material separation techniques.

Thermoacoustics basics
----------------------

Adiabatic compression and expansion of gases changes their temperature
as well as their pressure, so every sound wave generates local
temperature oscillations.  Sound waves in air can reach a few
megahertz over meter-like distances and much higher amplitudes than we
are commonly used to, up to nearly an atmosphere even at atmospheric
pressure, and higher amplitudes still at higher pressures.  Let’s
consider a sinusoid.

Normally the oscillation of pressure, volume, and temperature is
almost perfectly in phase; especially at high frequencies, the hot
compressed gas only has time to lose a tiny amount of heat through
radiation or conduction before it’s being decompressed and thus
cooled, and once cold it only has time to *gain* a tiny amount of heat
by absorbing radiation or conduction before the cycle starts again.
In a traveling wave, 90° out of phase to this oscillation of volume
(and, to a very good approximation, temperature and pressure, XXX no
that’s exactly backwards), is an oscillation of displacement.
Assuming no overall movement, a parcel of air passes its average
position when it is either maximally small or maximally large.
Suppose the wave is moving to the right.  When it is maximally small,
the parcel is moving at its peak velocity to the right; when it is
maximally large, it is moving at its peak velocity to the left.
Halfway in between, when its pressure is changing fastest, its
velocity is zero, but its displacement is at its maximum.

That is, the velocity oscillation in the direction of wave travel is
180° out of phase with the volume oscillation (and, to an excellent
approximation, pressure and temperature, XXX no that’s exactly
backwards); the velocity oscillation in the direction opposite wave
travel is in perfect phase with the volume oscillation.  The
displacement oscillation lags the velocity oscillation by 90°: the
displacement in the direction of wave travel is at its maximum when
the volume is at its average level and is expanding most rapidly,
and at its minimum (moving at maximum speed in the *opposite*
direction from wave travel) when the volume is at its average
level and is *contracting* most rapidly.

Because the displacement is almost exactly 90° out of phase with the
temperature, there is no tendency to transfer heat preferentially in
either direction.  When the parcel is at its extremal positions it is
at its average temperature, and when it is at its extremal temperature
it is at its average position.  So the average temperature of the
parcel at each position is the same.

Now, if we have some liquid or solid particles suspended in the air,
such as fine dust, they will move with it — effectively increasing its
density by a bit — but will not be subject to the same adiabatic
heating and cooling, since their volume changes with pressure orders
of magnitude less than the air’s does, basically a rounding error in
this context.  They *will* change temperature, but only because they are
exchanging heat with the air they are suspended in.  This will cause
the temperature oscillation to lag the pressure variation a little,
with the result that the volume variation (PV = nRT, thus P = nRT/V)
will not be precisely 180° out of phase with either of them, but a
little bit in between.  This means that the temperature oscillation is
no longer precisely in quadrature with the displacement oscillation,
and so there is a tendency to pump heat in one direction or the other.

This is the basis of two major thermoacoustic effects.  Typically,
instead of using dust, they use solid objects the gas can flow past,
which makes the heat pumping substantially more useful.  This allows
the moving gas to leave the heat behind.  The process is reversible,
so it can also be used as a heat engine, essentially a Stirling engine
where the mass of the air itself acts as a piston.  In either case,
there is a temperature gradient along the “regenerator”.  Many
thermoacoustic machines using these two effects are known.

Regular gas chromatography
--------------------------

Gas chromatography separates gases by mixing them into a mobile phase
that passes through a stationary phase (normally a liquid supported on
an inert solid) which preferentially adsorbs some of the gases,
slowing their passage.  Consequently the different gases arrive at the
other end of the chromatography column at different times.  This is
usually used for analysis rather than purification; it’s kind of
inherently a batch process.

Thermoacoustic mixture separation
---------------------------------

But perhaps we can use this same thermoacoustic effect to get a sort
of continuous-flow gas chromatography or rapid fractional
distillation.  When the stationary phase (for example, a powder bed or
a liquid on the surface of a powder bed) is at its peak temperature,
it will tend to free the gases adsorbed onto or absorbed into it; if
this is immediately followed by movement in a given direction, all
those adsorbed gases will tend to move in that direction.  Then, at
the lower temperature in the other part of the acoustic cycle, all the
gases will move back in the opposite direction — but those that are
more strongly adsorbed will do so less.  This cycle can happen at
kilohertz to megahertz frequencies, so even a small difference can be
used. I suspect that by feeding in gas in the center of such a column,
you should be able to get one gas out one end of the tube and another
gas out the other.  The net direction of movement for each gas will
depend on the degree to which its adsorption or absorption varies
across the temperature range reached by the sound wave, and on the
average axial flow in the tube, which is controlled by the difference
in the amounts of gas drawn off at each end.

An inefficient version of this, without the packing, was [discovered
by accident in 2000 at Los Alamos][0].

[0]: https://permalink.lanl.gov/object/tr?what=info:lanl-repo/lareport/LA-UR-01-3040 "Separation of Gas Mixtures by Thermoacoustic Waves, by D. A. Geller, P. S. Spoora, and G. W. Swift"

If gas is drawn off at several points along the length of the column
then the average flow rate through the column will change at each such
point.  I think this permits the removal of certain components, giving
a sort of horizontal thermoacoustic version of fractional
distillation.

Liquid separation
-----------------

But what if you want to do this with liquid chromatography instead?
You can’t rapidly heat and cool the liquid or the solid stationary
phase by running sound waves through them; they aren’t compressible
enough.  But there are other possibilities.

The most obvious one is that if you’re doing TLC, thin-layer
chromatography, you can heat and cool your TLC layer by heating and
cooling the plate.  But there are other more interesting
possibilities.

Fractal recuperator reciprocating liquid purification
-----------------------------------------------------

I’ve previously written about synthetic *retia mirabilia*, fractal
recuperator-style heat exchangers where the heat-exchange capillary
surface, which separates the hot and cold reservoirs, is convoluted
like the surface of a cauliflower in order to maximize its area.  Such
a device could be used directly for this kind of separation.

Consider it to have four main spaces: A1, A2, B1, and B2.  A1 is
connected to A2 by capillaries through the cauliflower surface, and B1
is similarly connected to B2 by separate capillaries through the
cauliflower surface.  The A and B spaces are not connected by mass
flow at all, but intimately exchange heat through the capillary walls.
A1 and B1 are always cold; A2 and B2 are always hot.  So there is a
thermal gradient along the capillaries.  B1 initially contains the
liquid mixture we would like to separate, and A contains some
arbitrary fluid, either liquid or gas.  The cycle goes as follows:

1. We add, say, ten times the volume of the capillaries to A1.  This
makes the capillaries cold and causes hot liquid to come out the A2
spout.  Because the B capillaries become cold too, more components of
the liquid adsorb to them.

2. We add, say, a tenth of the volume of the capillaries to B1.  This
moves some liquid into B2, from the ends of the capillaries.

3. We add the same amount of liquid as in step 1 again, but now to A2
instead.  This makes the capillaries hot and causes cold liquid to
come out the A1 spout.  Because the B capillaries are now hot, less
components adsorb to them.

4. We add, say, a twentieth of the volume of the capillaries to B2.
This moves some liquid into B1 from the ends of the now-hot
capillaries.

So, components of the B liquid that are more than twice as mobile at
the hot temperature tend to move from B2 to B1, while components that
are less than twice as mobile tend to move from B1 to B2.  And on
average the liquid goes through twenty of these “fractional
distillation” cycles in the capillaries before making it all the way
from B1 to B2.  By adjusting the ratio of the amounts in steps 2 and
4, we can adjust “twice” to whatever ratio we want; by adjusting the
amount added in step 2 we can adjust the number of cycles and thus the
purification.

It probably isn’t possible to do this at macroscopic scales at more
than a kilohertz or two, and maybe much less, which is a big
disadvantage compared to the thermoacoustic approach above.

An H-bridge
-----------

Another alternative approach is an H-bridge, like the motor control
circuit.  Here you have five tubes forming the shape of a capital H.
The horizontal tube is your packed column.  Fluid moves both left and
right in it, but in the vertical tubes it only ever moves down.  The
vertical tubes have valves.  The left vertical tube is connected to a
source of cold liquid at the top, and the right vertical tube to a
source of hot liquid.  First you open the upper left and lower right
valves, causing cold liquid to flow down, flow rightward through the
packed column, and then flow down the right bottom leg.  Then you
close these valves and open the other two, so hot liquid flows down
the right top leg, leftward through the packed column, and down the
left bottom leg.

This works somewhat similarly to the *rete mirabile* approach
described above, but requires much stronger adsorption or absorption
onto the stationary phase to be of any use, because a useful amount of
temperature change has to move through the column faster than the
eluent.  It has the advantage, however, that it does not require
exotic fabrication technology.

(It also may be reasonable to add the materials to be separated in the
center of the column, as above.)

The Hot Chocolate Effect
------------------------

It may be possible to use thermoacoustic techniques with liquids by
filling the liquids with bubbles.  As observed in the Hot Chocolate
Effect, even fairly small admixtures of bubbles give the liquid
compressibility of the same order of magnitude as the gas in question,
but because the mixture’s density is still the same order of magnitude
as the liquid, sound-wave speeds are extremely slow, and displacements
are extremely small for a given sonic power level.  It seems like this
might make these methods less effective with bubbly liquids.

Pressure-swing solidification
-----------------------------

An alternative mechanism for varying the mobility of ingredients of a
mixture is to use the change in the pressure (rather than the
temperature) out of phase with the displacement.  With notable
exceptions like boric acid and water, most liquids reduce in volume
when they solidify, so they tend to solidify under higher pressure and
liquefy under less pressure.  So by repeatedly moving a fluid to the
right, compressing it, moving it to the left, and rarefying it, you
can get separation of the components that more easily solidify under
pressure.  (It will work for liquids like boric acid as well, just in
reverse.)  This will probably work a lot better at near-gigapascal
pressures and pressure swings, which is a very loud sound indeed.

One advantage of this version of the approach are that the stationary
phase and the mobile phase can be chosen to have nearly the same
acoustic impedance, which is not feasible with gases (except perhaps
at extraordinary pressures), which means that interfaces will scatter
the sound less, so the sound will be attenuated less.  Another
advantage is that liquids and solids can transmit much higher
frequencies of sound than gases can.

The key challenge in this variant is probably going to be getting
enough displacement to outrun diffusion.

Packed columns?
---------------

All these “packed columns” might be better as a honeycomb of narrow
parallel tubes, a configuration already commonly used for catalyst
support, in part to reduce acoustic losses.  You could imagine that it
would also reduce turbulence losses, but if your passages are wide
enough to permit turbulence, your system probably has bigger problems.
