There’s discarded styrofoam on the streets every day, so I want a
hot-wire foam cutter, for which the best wires are stainless steel.
But the fine wires I have handy here are not stainless steel: they are
real steel and copper.  Also I have nichrome wires, but they are not
fine.  Which is okay, neither am I, but it’s not ideal.

These copper wires are about 100μm in diameter (38 AWG) and are
strands of wire from a stranded power cable.  I’ve calculated that
this should be about 2.2Ω/m, and simple measurements seem to confirm
that this is in the ballpark.  Copper oxidizes in air, but only quite
slowly below about 300°, and [isotactic polystyrene melts at only
240°][0].  And molten foam charring and then burning on the surface of
the wire ought to reduce the copper oxide thus formed back to copper,
rather than oxidizing it.

[0]: https://en.wikipedia.org/wiki/Polystyrene

I can probably use a high-frequency buck converter to efficiently
produce a high enough current through these wires to heat them into
the appropriate temperature range, and I could probably even servo it
with the resistance of the wire; see [Thermistors].

[Thermistors]: thermistors.md

So how many amps do I need to reach, say, 270°?  That depends on the
black-body emission of the wire, which must balance the joule heating
from its resistance at that temperature.  2.2Ω/m is also 2.2 W/m/A²,
which means that, say, at 100 amps, it would be dissipating 22000 W/m,
but at 10 amps, only 220 W/m.

Copper itself doesn’t have super high emissivity, but the charred foam
or black copper oxide that will undoubtedly cover it in use has
virtually 100% emissivity, so at 270° it will emit about 4900 W/m².
π 100 μm is of course π 100 μm²/μm, or 0.314 mm²/mm.  4900 W/m² is
about 1.5 mW/0.314 mm², so that works out to about 1.5 mW/mm of
length, or 1.5 W/m.

So if you have, say, 100 mm of this wire, you need about 150 mW to
maintain it at 270° in the face of radiation (a fact independent of
the wire material except for its emissivity, as well as the source of
the heat), and maybe a few times that when foam is cooling it down.
As mentioned above, this is 1.5 W/m (a fact additionally independent
of its length), and so for copper we need about 830 mA, which is again
independent of the length.  The only difficulty is that, for 100 mm,
our resistance is only 220 mΩ, so our 830 mA must be delivered at
about 180 mV.

If we want to deliver this with a buck converter from a 5-volt power
supply such as a USB charger or battery pack — and 150 mW or even
450 mW is a totally reasonable power level for that — we need about a
3.6% duty cycle on the pass transistor.  That is, 96.4% of the time,
the freewheel diode or equivalent will be supplying the (average)
830 mA through the inductor, and the transistor will only be supplying
it 3.6% of the time.

But wait!  The resistivity I was using to compute 2.2Ω/m is the
*room-temperature* resistivity of copper.  As noted in [Thermistors],
copper’s room-temperature coefficient of resistivity α is about
.0039Ω/°Ω, and its resistance is quite linear with temperature up to
over 300°, so actually at 270° we should expect the resistance to be
4.4Ω/m.  This drops our current requirement to 580 mA but actually
increases the voltage requirement to almost 260 mV.  This permits the
use of a less extreme duty cycle, about 5.2%.

How could you use the wire’s resistance to control the buck converter?
When its resistance is low, like 220 mΩ instead of 440 mΩ, you want to
feed it more current so it heats up; and if its resistance gets
higher, you want to feed it less.  A lot less, in fact, if it gets
more than a little bit higher, since at 300° the copper won’t last
long.

There are other desiderata, though.  You never want to use more than
2.5 W, both to stay within the original USB spec and because that’s an
absurd amount of power to dump into 7 mg of copper; it should get you
up to temperature in less than a second and allow you several
milliseconds to respond to stop overshoot.  And you never want to dump
*much* more than the steady-state 150 mW in there once the wire is
decently hot, because it might just be locally cooled by water or
something, and you might overheat the rest of it.  And you never want
to try to push the voltage *or current* too high, because the output
might have gotten open-circuited (because the wire broke) or
short-circuited.  This suggests that you might want to shift into
discontinuous conduction mode for shutdown cases, so that both the
current limit *and* voltage limit can be low, and have some parallel
bleeder resistor that will drop less than 100 mW at whatever max
voltage is acceptable, thus keeping the buck’s output from soaring.
For example, a 10Ω 0805 resistor would pass 70 mA at 700 mV, thus
49 mW.

270° is 1.55 W/m; 280° is 1.67 W/m; 290° is 1.79 W/m; 300° is
1.92 W/m.  So as long as we don’t have too much insulation around the
wire (of something other than foam!) we have about a 24% power
cushion, where if we’re sending in too much current we just get a
too-high temperature instead of actually melting the wire.  This
amounts to about an extra 11% of current.  In theory it might be
possible to detect “abnormal heat dissipation” resulting from, say,
cold spots, and that might work better than hoping the cold spot isn’t
more than 24% of the wire.

This level of complexity makes me think using a microcontroller might
be easier than trying to design an analog circuit that does everything
automatically with separate components.

2.5 W with 220 mΩ is 3.37 A or 740 mV, so the maximum current and
voltage definitely shouldn’t be higher than that.

Bang-bang
---------

The simplest possible control scheme for this, other than just relying
on the wire’s power dissipation, would probably be bang-bang control:
heat the wire at epsilon over the desired voltage level, say 280 mV
instead of 260 mV, until the resistance increases to the expected
level of 440 mΩ.  Then cut off the supply for a while.

It would be ideal if you could just leave it turned off until the
resistance has dropped by some amount of hysteresis, but you can’t do
that because you don’t know what the resistance is when the power
supply is turned off, so maybe it would be reasonable to use a period
of time that’s not so long that the wire has cooled down too much.  At
150 mW and 7 mg and [copper]’s room-temperature heat capacity of
24.440 J/mol/K and 63.456 g/mol, thus 385 mJ/g/K, thus 2.7 mJ/K, thus
18 ms/K, it seems like 10 ms would be a reasonable time to turn off
for.

Incidentally, this works out to 56 K/s, which means that even without
using multiple watts at startup, you only have to wait about 5 seconds
before it’s up to temperature — less, really, since using a constant
voltage means the lower 2.2Ω cold resistance will give you 600 mW
initially.

[copper]: https://en.wikipedia.org/wiki/Copper

### Series sense resistors are bad ###

To measure the resistance, one way would be to put a sense resistor in
series with the cutting wire, and you probably want it to have a
resistance that’s small compared to the cutting wire itself so it
doesn’t get too hot — say, a few milliohms instead of a few hundred
milliohms.  You probably can’t really do this by adding a voltage
probe to the cutting wire itself, because your probe section will heat
up too, and the voltage you read won’t depend on the wire’s
temperature, just where the probe is and what the total voltage is.
If ±10° is an acceptable error, then the cutoff resistance can range
from 426 mΩ to 443 mΩ, about a 4% error (±2%), some of which can come
from the sense resistor, say about 2% (±1%).  Maybe 5 mΩ ±1% would be
suitable.

Probably the way to deal with this precision requirement is to make a
current-measurement resistor out of the same wire as the cutting wire,
but using about 50 strands of it, cut to, say, 113.6 mm ±1%, to get
5 mΩ.  Or you could build or buy a milliohmmeter before you try to
build the foam cutter.

The other problem with the series-sense-resistor approach, though, is
that you also have to measure its voltage to within ±2%, or better ±1%
to avoid spending the entire error budget on voltage measurement and
having none left over for, I don’t know, variation of ambient
temperature.  But its voltage is 580 mA times 5 mΩ, or 2.9 mV, and 1%
of that is 29 μV.  That’s not a lot of noise immunity for a switching
power supply running on 5 volts.

### Instead, measure the current with the bleeder ###

Above I said that you need a bleeder to keep the output from soaring
when the wire breaks.  If we make the dubious assumption that the
input voltage and buck duty cycle are precisely constant, then in CCM
the average output voltage will also be precisely constant, but the
current will vary according to the load.  So, if we suddenly
open-circuit the output with a second transistor, the output inductor
will suddenly be feeding only the bleeder, but its current can’t
change instantaneously, so the voltage there will suddenly jump
enormously, potentially even higher than the 5-volt input.  This new
peak voltage will immediately start to fall, but the peak should
fairly precisely be the output current multiplied by the bleeder.

And if the peak is too low, then the cutting wire has too much
resistance, so we should leave it turned off for 10 ms to maybe cool
down.

For this to give us a precision measurement, the bleeder needs to stay
at a constant temperature, and so can’t dissipate much heat during
normal operation.  Let’s spitball 1 mW.  At 280 mV, this would suggest
using a 78.4Ω resistor.  If that 280 mV was previously feeding the
220-mΩ cutting wire, it will have been producing 1.27 A (!), and if
this resistor suddenly finds itself carrying 1.27 A, it’s going to
spike up to a somewhat dismaying 99.6 V.  If the wire is at
temperature, and thus 434 mΩ, then it’s only 645 mA, and we see only
50.6 V.  So when the inductive spike falls below 50.6 V then we need
to turn off the wire for a while, maybe by just leaving the
spike-generation transistor turned off.  Well, at least we don’t have
to worry about microvolt noise anymore!

At these voltages, a peak detector consisting of a diode and a
capacitor will be pretty precise; the variation in the diode’s forward
drop is going to introduce maybe 0.3% error at 50.6 V, and the
capacitor’s capacitance doesn’t matter for the voltage peak as long as
it’s not so large that the inductor’s current droops significantly
(more than 1% I guess?) before the capacitor is charged.

Then we can just divide down the capacitor’s sample of the peak
voltage to some kind of reasonable level to see if it’s so low that we
should leave the output turned off for a while.  Or, from a different
perspective, to see if it’s high enough to turn the cutting wire back
on.  And the divider network, though it also needs to be precise to
sub-percent levels, can also double as a bleeder resistor to drain the
peak-detector capacitor so that the next peak actually gets detected.

This suggests also using the peak-detector draining to time the time
until the next sample — that is, when the capacitor has drained far
enough, we turn off the output spike-generation transistor, just as we
would if the capacitor hadn’t charged far enough in the first place
(because the current was too low, because the output resistance was
too high, because the wire was too hot).  We probably need some kind
of Schmitt-trigger action to make sure the transistor turns off
quickly instead of gradually; I gotta think about how to do the
comparison of the peak-detector output to the reference voltage by
using something simpler than a differential pair.

We can, of course, vary the bleeder/measurement resistor to any
similar convenient value; we just have to vary the peak-detector
divider factor proportionally.

The 78.4Ω example value results in 3.6 mA of bleeder current at the
normal 280 mV, which seems like it ought to be enough to keep the
output from soaring.  I guess I don’t really know how to calculate
that, but it’s a pretty non-negligible amount of current.

Or maybe just use the wire voltage for feedback
-----------------------------------------------

On ##electronics Famine suggested instead feeding the wire with a
constant-current supply and servoing off the wire’s voltage; they gave
an example linear circuit that drives an output Darlington off an
op-amp which compares to a reference voltage from a voltage divider.
This still means you need measurement precision of a couple of
millivolts, but in a linear circuit like the one they designed, that’s
totally reasonable, especially if it’s running off a battery or
something instead of a USB power bank; and it avoids having hundreds
of volts anywhere in the circuit.  The only real drawback I see is
that it can’t step up the current from what the power supply can
provide, the way a buck converter can, and at any reasonable input
voltage it burns most of the power in the output Darlington.

Here’s [Famine’s circuit in the format of Falstad’s circuit
simulator](https://tinyurl.com/y24h6gay):

    $ 1 0.000005 0.529449005047003 50 5 43
    R -960 -160 -960 -192 0 0 40 10 0 0 0.5
    r -960 -48 -960 -96 0 1000
    t -960 -48 -912 -48 0 -1 1.271599532576758 -0.586456396571938 100
    r -960 0 -960 48 0 84
    r -912 -32 -912 48 0 1000
    t -912 -32 -864 -32 0 1 -1.858055929148696 0.6422963295577914 100
    w -912 -96 -912 -64 0
    w -864 -48 -864 -96 0
    w -864 -96 -912 -96 0
    w -864 -16 -864 48 0
    w -864 48 -912 48 0
    w -912 48 -960 48 0
    g -912 48 -912 80 0
    r -960 -96 -960 -160 0 1000
    34 default-led1 0 9.32e-11 0.042 4.6 0
    162 -960 -48 -960 0 2 default-led1 0 1 0 0.01
    w -864 -96 -832 -96 0
    w -960 -96 -912 -96 0
    r -832 -16 -832 -96 0 20000
    r -832 48 -832 -16 0 2200
    w -864 48 -832 48 0
    207 -832 -16 -800 -16 4 WREF
    207 -832 -96 -800 -96 4 REF
    207 -832 -128 -864 -128 4 VIN\p
    207 -320 0 -272 0 4 VIN-
    t -400 -128 -400 -160 1 1 -8.961181213242781 0.7739571557760819 100
    R -416 -160 -416 -192 0 0 40 10 0 0 0.5
    w -384 -160 -320 -160 0
    r -320 0 -320 48 0 0.265
    g -320 48 -320 64 0
    207 -832 -128 -800 -128 4 WREF
    207 -416 -48 -416 -16 4 OUT
    w -544 -128 -544 -160 0
    w -576 -160 -544 -160 0
    w -720 -80 -720 -112 0
    w -592 -80 -592 -48 0
    w -720 -80 -592 -80 0
    r -720 -160 -720 -112 0 10000
    w -720 -48 -720 -80 0
    w -576 -112 -576 0 0
    g -544 -96 -544 -64 0
    r -576 -160 -720 -160 0 3000
    t -576 -112 -544 -112 0 1 -1.0940530012757717 0.6209041892124895 100
    g -592 48 -592 64 0
    g -720 48 -720 64 0
    r -576 -112 -720 -112 0 2200
    w -720 0 -576 0 0
    w -592 16 -592 -16 0
    w -720 0 -720 -16 0
    w -720 16 -720 0 0
    r -592 16 -592 48 0 10000
    r -720 16 -720 48 0 1000
    207 -624 -32 -640 -32 4 VIN-
    t -624 -32 -592 -32 0 -1 -0.550781216679921 -0.574071418383921 100
    t -688 -32 -720 -32 0 -1 -0.3620094258350334 -0.5800382859876023 100
    207 -544 -160 -496 -160 4 OUT
    207 -688 -32 -672 -32 4 VIN\p
    R -720 -160 -720 -192 0 0 40 10 0 0 0.5
    t -416 -96 -416 -128 1 1 -8.306594149635059 0.6545870636077218 100
    w -432 -128 -432 -160 0
    w -432 -160 -416 -160 0
    r -416 -96 -416 -48 0 220
    370 -320 -160 -320 0 1 0 0

Alternative energy sources
-------------------------

Using only 150 mW means you could use even a CR2032 coin cell;
Energizer suggests theirs has under 10Ω of internal resistance for
much of its lifetime, and under 20Ω until it’s almost dead.  3 volts
at 20Ω is 150 mA, which is half a watt.  The battery won’t last long
at that kind of drain.  Its typical capacity is given as “235 mAh”, or
in SI units 846 coulombs, or about 2.5 kJ, but you’ll be lucky to get
a tenth of that at these high drains.  So the battery might only last
a few minutes to half an hour or so.

Various kinds of capacitors can hold a few hundred joules as well XXX