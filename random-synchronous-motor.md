I just ripped apart a microwave, and one of the things I got out of it
was a turntable motor.  This turns out to be a 5-watt, 5 RPM,
synchronous 240VAC gearmotor.  This is one of the few occasions where
it really doesn't matter which way the motor turns, so in fact they
used a synchronous motor that turns in a randomly different direction
each time, depending I suppose on where it was when it stopped.

Even if you couldn't sense its position well enough to predict which
direction it would turn at startup, you still might be able to use it
for motion control with closed-loop feedback, using the following
scheme: when you start up the motor, if it's going the wrong way, wait
a random fraction of a rotation (of the motor, not the gearbox output
shaft), turn it off, and, after enough time for it to stop, turn it on
again.

You could take this simple control scheme one more meta level to
flagellate bacterium behavior: while the conditions for a machine
continue to improve, run the motor continuously; while conditions
remain the same or get worse, run the motor intermittently.  Connect
the motor to a mechanism such that, in one direction of rotation, the
machine moves in a straight line in whatever direction it's pointed,
but in the other direction of rotation, the machine wanders around
randomly.  When the motor is running intermittently, it will sometimes
go in a straight line, and sometimes wander, but once it happens to be
moving in a straight line that improves the situation, it will
continue.

Unfortunately, such a mobile machine would probably be
battery-powered, and running a synchronous AC motor off DC requires
about as much circuitry as the above feedback scheme would take to
implement.
