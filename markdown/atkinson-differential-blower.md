The four-stroke Atkinson differential engine uses a clever arrangement
of linkages to move its two pistons in a single cylinder, out of phase
with one another.  It can use reed valves, like a two-stroke engine;
both the intake valve and the exhaust valve are at the same end of the
cylinder.

The orthodox Atkinson cycle
---------------------------

The sequence is as follows.  Suppose the intake and exhaust valve are
at the right end of the cylinder, and the pistons are close together,
with the intake and exhaust valve between them.  First the pumping
piston, on the left moves to the left, opening the intake valve and
sucking air and fuel into the cylinder.  Then the working piston, on
the right, moves to the left, covering the valves and compressing the
fuel-air mix against the now-stationary pumping piston.  Then the
spark fires, and the working piston moves to the right, providing the
power stroke.  Finally the working piston passes the exhaust valve,
the hot gas escapes through it, and the pumping piston follows it to
the right, preparing to pump in the new fuel-air mixture.

Quiet piston blowers
--------------------

On ##electronics cloudevil was talking about piston-powered blowers,
displacing multiple liters of air per stroke, suggesting that they
could be much quieter than traditional types of blowers, though of
course they’ll still produce turbulent airflow.  But such a blower
will fail to be quiet if it’s using poppet valves or reed valves,
since those produce an impulse every time they open and close.

You could imagine using a round, triangular, or teardrop-shaped port
or hole in the side of a cylinder as a valve; when the piston passes
over it, it opens or closes, but not impulsively.  But this has two
problems.

First, in the Atkinson engine design, the intake and exhaust valves
are at the same end of the cylinder, so if there’s no reed valve or
anything, they’ll be open at the same time.  This is no way to make an
air pump.

Second, if there’s a pressure difference across the valve as it opens,
the airflow through it will still start suddenly and with a lot of
turbulence, so it will be noisy, though maybe less so than a reed
valve.

Both of these problems can be solved by moving the valve openings to
opposite ends of the cylinder and redesigning the cycle for pumping
without such events.

First, the left cylinder is at the left end of the cylinder, just to
the left of the intake port, and the right cylinder is to the right of
the intake port.  Second, the right cylinder moves to the right,
almost to the exhaust port, at the right end of the cylinder; in this
way air is sucked into the cylinder from the intake port.  Third, both
cylinders move in unison to the right, first closing the intake port
and then opening the exhaust port.  Fourth, the right cylinder stops,
while the left cylinder continues moving to the right, expelling the
air through the exhaust port.  Fifth, both cylinders move in unison,
close together, back to the left.

These movements can easily be scripted by cams to minimize the
bandwidth of the pistons’ movements, thus eliminating the direct
production of sound above, say, four times their movement frequency,
which might be 2 Hz.  Then only turbulence and surface roughness are
left as noise sources.

Why four times?  If the pistons moved back and forth in unison a
single time, or with a simple difference in phase, they could move in
a perfect sinusoid, thus generating no sound from their sheer movement
at frequencies higher than their movement frequency.  But the movement
I described above is not simply sinusoidal, so it would involve some
harmonics.  8 Hz is inaudible, but 20 Hz or more might be audible and
highly annoying.  Of course air turbulence and surface roughness will
generate higher-frequency noise no matter what the cylinders’
movement.

You might be able to find a lower-displacement purely-sinusoidal
movement pattern with the right characteristics — most crucially that
the cylinders be closer together when moving leftwards than when
moving rightwards, and the same distance apart when the intake port
closes and when the exhaust port opens.  If a single sinusoid can’t do
the job, you might be able to find something that only uses two or
three harmonics rather than four, and thus enable higher operating
frequencies while remaining purely infrasonic, maybe up to 5 Hz or 10
Hz.  But I’m confident that with four harmonics you can do it.

Engines
-------

You can take the same approach and apply it to the problem of making
an engine, too, in the sense of a device that converts heat energy
into mechanical energy.

The most direct approach is to feed steam or pressurized air into the
intake; then the suction stroke becomes the power stroke, but you
still have a sharp noise when the pressurized air gets over to the
outlet port, and that noise of course represents wasted energy.
Similarly, when the small space between pistons moves over the intake
port, the pressure in it is the exhaust pressure, which is low.  If
you revise the cycle somewhat so that the space between pistons is
zero when they open the input port, then continues expanding after the
input port is closed, you can get all the adiabatic energy in the
input working fluid.  Alternatively, rather than making the space
zero, it could simply be smaller than its size when the exhaust port
closed by an amount sufficient to bring its pressure up to the intake
pressure.

However, if you want it to be a standard four-stroke
internal-combustion engine, you need the following cycle:

1. Intake: pistons separate, pulling in some fuel-air mixture from the
   intake port.
2. Close intake: pistons move in unison to the right, closing the
   intake port.
3. Compression: pistons approach one another, compressing fuel-air
   mixture.
4. Expansion: spark fires, pistons separate, allowing expansion.
5. Open exhaust: pistons move in unison to the right, opening the
   exhaust port.
6. Exhaust: left piston continues moving to the right, reducing volume
   of chamber and expelling exhaust gases.
7. Close exhaust: pistons move in unison to the left, closing exhaust
   port.
8. Return: pistons move in unison to the intake port.

This requires some fancy camwork; the followers pressing on the cams
during the expansion stroke is what drives the engine forward.
Alternatively it may be possible to design a linkage that does all
this, which may be beneficial in terms of being easier to adjust.

Adjustment of the cycle might be useful for a variety of reasons:

1. Exhaust gas recirculation: by including some exhaust gas from the
   previous stroke in the mix, you can reduce pollution by lowering
   combustion temperatures and improve engine efficiency.  In this
   engine you can simply not bring the pistons all the way together,
   so some exhaust remains trapped between them when they return to
   the intake.
2. Atkinson cycle: by using a greater expansion ratio than compression
   ratio — that is, by compressing the gas less in step #3 than the
   combustion products expand in step #4 — you can improve energy
   efficiency.  Any pressure difference remaining between combustion
   products and the outside world when the exhaust port opens in step
   #5 represents a waste of energy and a source of noise.
3. Anti-Atkinson cycle: by using a greater compression ratio than
   expansion ratio, you can get more power at the expense of lower
   efficiency, more noise, and less complete combustion.
4. Throttling: by increasing or reducing the amount of fuel-air mix
   brought into the cylinder on each stroke, we can increase or reduce
   the engine’s power output further.  This eliminates the need for a
   butterfly or other throttle valve and the associated vacuum losses.

A second cylinder configured as described above can be used to harvest
further energy from the exhaust, in the manner of double-expansion or
triple-expansion steam-engines; this will also reduce noise further,
as the second cylinder serves as a sort of muffler for the first.
(You could also use such an engine as a muffler for a conventional
internal-combustion piston engine, surely not a novel idea, since
triple-expansion steam-engines go back generations.)

An engine with two or three cylinders cascaded in this way can be
fitted with valves to redirect the flows of gases to answer demands
that change from moment to moment: now a triple-expansion engine with
a single combustion chamber, one cylinder feeding into the next, for
greater efficiency, now a three-cylinder engine with all three
cylinders burning fresh fuel for greater power.

By virtue of moving the expanding gas chamber down the cylinder as it
expands, the loss of heat to the cylinder walls is reduced — while
this benefits an internal-combustion engine less than a steam-engine,
it may pose difficulties in keeping the hotter parts of those walls
lubricated.

As the space between the pistons can be reduced indeed to zero,
requiring no accommodation for the opening of valves, the spark-plug
is redundant in these classes of engines; it can be designed to ignite
purely by adiabatic heating as in Diesel’s engine.
