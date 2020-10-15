The four-stroke Atkinson differential engine uses a clever arrangement
of linkages to move its two pistons in a single cylinder, out of phase
with one another.  It can use reed valves, like a two-stroke engine;
both the intake valve and the exhaust valve are at the same end of the
cylinder.

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

On ##electronics cloudevil was talking about piston-powered blowers,
displacing multiple liters of air per stroke, suggesting that they
could be much quieter than traditional types of blowers, though of
course they'll still produce turbulent airflow.  But such a blower
will fail to be quiet if it's using poppet valves or reed valves,
since those produce an impulse every time they open and close.

You could imagine using a round, triangular, or teardrop-shaped port
or hole in the side of a cylinder as a valve; when the piston passes
over it, it opens or closes, but not impulsively.  But this has two
problems.

First, in the Atkinson engine design, the intake and exhaust valves
are at the same end of the cylinder, so if there's no reed valve or
anything, they'll be open at the same time.  This is no way to make an
air pump.

Second, if there's a pressure difference across the valve as it opens,
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
bandwidth of the pistons' movements, thus eliminating the direct
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
generate higher-frequency noise no matter what the cylinders'
movement.

You might be able to find a lower-displacement purely-sinusoidal
movement pattern with the right characteristics — most crucially that
the cylinders be closer together when moving leftwards than when
moving rightwards, and the same distance apart when the intake port
closes and when the exhaust port opens.  If a single sinusoid can't do
the job, you might be able to find something that only uses two or
three harmonics rather than four, and thus enable higher operating
frequencies while remaining purely infrasonic, maybe up to 5 Hz or 10
Hz.  But I'm confident that with four harmonics you can do it.
