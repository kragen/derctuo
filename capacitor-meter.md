The project in [Multimeter Metrology](multimeter-metrology.md) is a
bit large to tackle all at once.  So I think it would be good to start
with a manageable subset.  In particular what I want is something to
use for measuring capacitors in the 47 pF to 1000 μF range to within
about 1%.

A basic design
--------------

If you put a string of two resistors between analog switchable pins A
and B of a microcontroller, and a capacitor between the junction
between the resistors and ground, then you have the following
configurations available:

- pin A output high, pin B input: capacitor charges through resistor
  A, being continuously watched;
- pin A output low, pin B input: capacitor discharges through resistor
  A
- pin A input, pin B output high: capacitor charges through resistor B
- pin A input, pin B output low: capacitor charges through resistor B
- pin A high, pin B low: capacitor charges to Ra/(Ra+Rb), can be
  measured later
- pin A low, pin B high: capacitor charges to Rb/(Ra+Rb)
- pin A low, pin B low: capacitor discharges through Ra||Rb
- pin A high, pin B high: capacitor charges to Vcc through Ra||Rb
- pin A input, pin B input: capacitor discharges through its internal
  leakage resistance

All of these measurements are essentially ratiometric, so we don't
care about the power-supply or reference voltage as long as it isn't
too noisy or so high the capacitor blows up (which a double-layer
capacitor might.)

Ideally, for component ID, we could go through all 9 measurements in
50 ms or so for capacitors anywhere in range.  If Ra and Rb are some
distance apart, like an order of magnitude or so, they can provide
different "scales"; but if they're *too* far apart, then several of
the cases won't be meaningfully different.  How far apart is "too far
apart" depends in part on the ADC's precision.

I can measure the resistances to an error of about 0.1% with my
existing multimeters (in [Multimeter
Metrology](multimeter-metrology.md) I found that they were about 0.03%
apart).  This doesn't tell me their temperature coefficients until I
heat them up or cool them down and measure them again.

If the resistance is too low, the capacitor will charge all the way to
its destination voltage too quickly to get a reasonable number of
samples, so higher sampling rates mean we can get by with lower
resistances.  If the resistance is too high, the capacitor won't
charge or discharge enough to measure reliably within the measurement
interval, so higher sampling precision means we can get by with higher
resistances.  Also, though, higher resistances make life harder for
the input circuits.

Larger numbers of resistors and pins could be deployed to get more
measurements.

I'm going to look at a few candidate chips.

ATTiny45
--------

I have 18 surface-mount ATTiny45s and 19 DIP ATTiny45s left over from
2006.  They run at 20 MHz on an external crystal, or up to 16 MHz on a
PLL from their internal oscillators, and have six GPIO pins, 4K of
Flash, 256 bytes of RAM, four single-ended ADC channels with a 10-bit
ADC, an analog comparator, and supposedly two differential ADC
channels with switchable 20× gain (although I wonder if maybe that
last feature is actually only in the ATTiny45/**V**).  The I/O pins
have a selectable pullup resistor of 20–50 kΩ.

The internal RC oscillator is only calibrated to ±10%, but the
datasheet says you can calibrate it to ±1% at a given temperature,
voltage, and frequency.  This would seem to eat the whole error budget
right out the gate.  But an external crystal eats up two of the six
GPIO pins.

Like most AVRs, at its maximum precision of 10 bits, it can only do
15 ksps, but it can run about 5× faster if you're willing to accept
more error.  It recommends an input impedance of 10 kΩ or less and
says its sample-and-hold capacitor is 14 pF, which makes it sound like
it's going to be pretty hard to measure down below 100 pF.

At 15 ksps we would get 45 samples in 5 ms.  If we were interested in
the *average* voltage, this would give us about a 12½-bit measurement,
which is plenty precise enough for our 1% objective.  Probably
measuring the charge or discharge rate will give us a 12-bit (0.1%
precision) measurement, which is still plenty.  (I haven't done the
analysis rigorously using the various error numbers from the datasheet
but I think these are in the ballpark.)

How fast of a time constant can we tolerate?  A measurement stops
being 1% accurate once it drops below 100 counts or so, about a tenth
of full range, so we have about ln 10 = 2.3 time constants, maybe a
bit more, to get our measurements.  (I'm glossing over the fact that
what we need to be 1% accurate is not the voltage but the timing,
which I don't think matters.)  I think we need at least 3 samples,
probably more, say 5 to be safe.  So we need the RC time constant to
be at least 2 samples, 133 μs, when we're using at least one of the
resistors.

Getting that at 47 pF would require a 2.8-megohm resistor, which is
inconvenient not only because my multimeters can't measure that high
(I'd have to do a four-wire measurement with two multimeters) but also
because the ATTiny45 datasheet specifies input leakage current of up
to 1 μA, though typically below 0.05 μA, and claims the analog input
resistance is typically 100 MΩ.  So compromising with 470 kΩ for the
low-capacitance-range resistor might be a better idea.  (I suspect
this might still be too high for the inputs to work reliably with.)

How low of a charge/discharge rate can we tolerate?  By the same
handwaving arguments above, I think we want to ensure that the
charging/discharging makes it up to 200 counts, about 20% of full
range, within the 5-ms measurement window.  ln 80% ≈ -.22, so that's
about 0.22 time constants, so τ can be up to 5 ms/0.22 ≈ 22 ms.  This
is disappointingly close to the minimal τ of 133 μs above, only 165
times longer.  If we want to get to 22 ms with 1000 μF, then we would
need a 22-Ω high-capacitance-range resistor, 20000 times smaller than
the low-capacitance-range resistor.  This would allow us to meet our
performance objective for 1000 μF, but at the cost of precision in the
midrange.

This situation probably calls for compromising like crazy to reach
acceptable performance.  Here are some candidate compromises:

- Use the internal pullup (on the 22-Ω pin) as an alternative
  medium-range pullup resistor.  This requires some kind of
  calibration procedure to find out what the internal pullup's E-I
  curve looks like (maybe not a straight line) and how it varies with
  temperature.
- Use a third pin with a third external resistor.  But keep in mind
  the chip only has six GPIOs!
- Use a longer measurement time for large capacitances so you can use
  a larger resistor.  470Ω would give you a time constant of 470 ms
  with 1000 μF, so you could reach the desired performance at 100 ms
  per measurement rather than 5 ms.
- Accept some performance degradation at the ends of the range.

However, there are some tactics not yet investigated:

- Maybe I'm being too pessimistic in some of my calculations.

- Can the differential-input mode with its selectable 20× amplifier
  help?  Maybe that would allow us to double the number of samples we
  can take for small capacitances, by turning the amplifier on when
  the signal gets low.

- Maybe if the capacitor is charging/discharging too fast, we could
  charge it to way outside the ADC range and measure how long it takes
  to discharge down into the range.  For example, we could charge it
  to 5 volts and use the 1.1V internal voltage reference as
  full-scale.  The 20× amp might help there too.  This obviously only
  works for

- You can configure the ATTiny45's PWM output to generate PWM levels
  at up to the system clock speed.  If running off a 16MHz clock you
  could get three PWM levels at 8MHz (0%, 50%, 100%), four at 5⅓MHz,
  five at 4MHz, and so on.  Even at 1600 kHz you have 11 levels.  I'm
  not really sure how this helps though.

- Maybe a higher sampling rate at the expense of precision would be
  good when the capacitance is small.

- Maybe even if the capacitor finishes charging or discharging in two
  or three samples, by repeating the measurement dozens of times we
  can get a precise enough average.

- For fast charging/discharging, the ATTiny45 also has an analog
  comparator, which can be configured as an interrupt source.  IIRC
  AVR interrupt latency can vary by up to two clock cycles, so this
  would give you timing information precise to ±62.5 ns on the RC
  oscillator or ±50 ns on a quartz crystal.  This would require an
  additional external voltage divider and I/O pin to provide the
  threshold voltage, but it gives you 1000 times tighter timing than
  the ADC.

Marcel Post's [postwiki article goes into some
detail](https://www.marcelpost.com/wiki/index.php/ATtiny85_ADC) on the
ADC on these chips.  (The ATTiny85 is the version with twice as much
RAM and Flash.)

It seems like it would be pretty difficult to meet the targeted
performance on the ATTiny45, but maybe possible.

(Actually I think the interrupt latency is not an issue because I
think there's an option to latch a timer value automatically when the
comparator fires, and I think you can run the timers at the chip's
full clock speed, but I need to check those out.)

### Time-domain sensing is a better option here ###

A much easier thing to do on this chip would be to do *only* the
comparator time interval measurement against a fixed threshold, using
a single pullup resistor from Vcc to one comparator pin and a divider
from Vcc to ground across the other comparator pin.  If you can
calibrate at startup or at regular intervals against a standard NP0
calibration capacitor then you ought to be able to compensate for the
internal oscillator's vagaries.  We can choose whatever threshold we
want, such as Vcc/2.  Then, if we want charging a capacitor to our
threshold to take at least 10 clock cycles at 16 MHz, we need a time
constant of at least 902 ns; if it's 47 pF we need at least 22 kΩ.  In
50 ms we can take 80,000 measurements, perhaps alternately of the DUT
and a reference capacitor, and average them, giving about 11 bits of
precision.

As the capacitor under test gets bigger, the measurements get slower,
until at 3.3 μF our time constant is 73 ms, so it takes the whole
50 ms to hit the comparator threshold.  This gives us a nice
high-precision measurement of 19.6 bits but it starts to get slow.  At
1000 μF it would take 15 seconds!

A possible solution to this problem is to generate the reference
voltage threshold with PWM instead of a voltage divider, thus using an
external RC filter instead of two external resistors.  At 160 kHz, you
can get 99 voltage theshold levels, taking times ranging from 4.61
time constants to 0.01 time constants to cross them.  So we can deal
with a time constant from 100× longer than our measurement period
(50 ms) down to 4.61× shorter than our shortest acceptable time
(spitballed as 625 ns above).  This means 136 ns (47 pF · 2.9 kΩ) to 5
seconds (1000 μF · 5.0 kΩ).  That's close enough to be reasonable.

(Alternatively the PWM can feed one of the ends of the DUT and we can
use a low fixed divider for the threshold.)

We probably want to RC-filter the PWM enough to get 1% ripple or less,
which is easy if it's just providing a reference voltage, which means
*the filter's* RC time constant should be at least 625 μs; but we want
it to be fast enough to be able to switch "ranges" while trying to get
the discharge time reasonable, at most a few milliseconds.  The
filter's time constant (and thus the capacitance and resistance) is
not critical to precision, but it needs to have low enough leakage
current that its voltage doesn't sag by more than 1% during a 6.25 μs
PWM cycle, but anything with such high leakage current that its own
leakage time constant is under 625 μs would be too poor to be sold as
a capacitor.  On top of this, we have the ATTiny's < 1 μA input
leakage current; at 22 nF and 6.25 μs this would be a 280 μV voltage
shift, which would amount to a 1% error on a 30-mV reference voltage,
so the filter capacitor should be bigger than 22 nF.  A 1 μF capacitor
and a 2.2 kΩ resistor would give a 2.2-ms time constant and worst-case
6.25 μV drift from the leakage current.

If the RC filter is feeding the DUT the problem of its design becomes
much more difficult.

[Atmel app note AVR400, document
doc0942.pdf](http://ww1.microchip.com/downloads/en/AppNotes/doc0942.pdf)
describes a similar design, using an AT90S1200 (lacking an ADC
entirely) with a crystal and an RC circuit to measure a voltage to 6
bits of precision.  In that case the "reference voltage" is the thing
to measure and the RC circuit just provides a sawtooth to compare it
to.

The analog comparator in the ATTiny45 can take its negative input from
any of the four ADC input pins, which enables us to switch between the
standard/calibration capacitor and the DUT on each measurement cycle,
which reduces the time available for decalibration to be produced by a
temperature shift, a supply voltage shift, a clock speed shift, or
resistor aging.  A temperature shift will also change the value of the
standard capacitor, though the NP0/C0G tempco is limited to 30 ppm/K,
and it may be possible to use the ATTiny45's on-chip temperature
sensor to compensate for that.

The [AVR TransistorTester
manual](https://raw.githubusercontent.com/svn2github/transistortester/master/Doku/tags/english/ttester_eng112k.pdf)
mentions that the offset voltage of the AVR's analog comparator limits
its accuracy on low-value capacitors.

So the final circuit is something like PWM1-2k2-AIN0-1μF-gnd;
Vcc-2k2-ADC0-DUT-gnd; Vcc-2k2-ADC1-1nF(NP0)-gnd.
That is, the reference-voltage pin is connected to the output of a
single-pole RC low-pass filter from PWM1 to ground, and the
multiplexed inputs ADC0 and ADC1 are connected to similar RC filters
that are "filtering" just V<sub>CC</sub>.  Some external
protection diodes and a protection resistor might be useful on the DUT
terminals to reduce the risk of damage from connecting a precharged
capacitor.

ATTiny2313
----------

The time-domain design is also applicable to the ATTiny2313, which has
PWM outputs and an analog comparator but no ADC at all; it has only
128 bytes of RAM.  But it has 18 GPIOs instead of 6.  I have a tube of
16 ATTiny2313 SOICs left over from 2006.

The ATTiny2313**A** is "the picoPower version".  [Amazingly, both
versions of the device are still in production, though the
manufacturer Atmel is
dead.](https://www.microchip.com/wwwproducts/en/ATtiny2313)

However, the ATTiny2313 has one serious drawback compared to the
ATTiny45 for this purpose: its analog comparator doesn't have
multiplexed inputs, so it *always* compares pin 12 and pin 13 (AIN0
and AIN1) (or pin 10 and pin 11 in the MLF/VQFN packages I don't
have).  So it can't recalibrate to a calibration capacitor evey
measurement cycle.  So getting reasonable precision on the ATTiny2313
(better than 1%, maybe better than 10%) would probably require using a
crystal with that design.

### A simpler design ###

A possibly better design, at least for the ATTiny2313, is
gnd-1kR-{1kR-AIN0 || 10nF(NP0)-GPIO1 || DUT-GPIO2}, with AIN1
connected to a filtered PWM output as before.  With GPIO2 tristated,
we can toggle GPIO1 to charge and discharge the 10nF NP0/C0G capacitor
through the 1kΩ ground resistor, and observe the charging process as a
falling voltage on AIN0.  When we're discharging it we may be in part
discharging through the clamping diodes of GPIO2 and AIN0 (and AIN0's
protection resistor), and also the voltage on AIN0 is negative, so
only the charge time is visible.

At 16 MHz the time constant for the specified values is 160 cycles,
which is plenty, and maybe a bit generous.

So then we tristate GPIO1 and do the same charge-discharge process
with the device under test, allowing us to observe its time constant
with the same 1kΩ resistor.  If it's 47 pF the time constant is 47 ns
(0.75 cycles), which is a bit on the low side, and if it's 1000 μF the
time constant is a full second, so we're stuck measuring it against
high thresholds like 0.9V<sub>CC</sub> (0.105 *τ*, 105 ms) and
0.99V<sub>CC</sub> (0.01005 *τ*, 10.05 ms).  But both of these values
seem reasonable.

The current decay curve through the GPIO pins depends on the
capacitance (and, a little, on the supply voltage), but initially it's
5 mA, assuming V<sub>CC</sub> = 5 V, which is a safe limit and
probably won't even heat up the chip much.  Heating up the chip might
be bad because it might be local and so unbalance the analog
comparator, introducing error.  Heating up the resistor might be a
bigger concern — the peak power there is 25 mW, and for large DUTs it
will dissipate essentially 25 mW all the time — but most of that
heating effect will be canceled out by alternately measuring the DUT
and our reference capacitance.

Since we're only measuring the τ₁/τ₂ ratio, by charging up to the same
max voltage through the same resistor measured against the same
voltage thresholds with the same clock, our measurement should be
independent of slow changes in any of these.

Checking out the datasheet (doc8246.pdf, 8246B-AVR-09/11).  The
microcontroller only has 128 bytes of RAM (p. 1), including the
return/interrupt stack, plus the 32-byte register file (p. 11), which
is mapped at addresses 0 through 0x1f, while the data SRAM is mapped
into the 128 bytes at 0x60 to 0xe0 (p. 17, clearly incorrect), and
also there are three spare bytes in the I/O register space, GPIOR[012]
(p. 17); all together, this is enough for a few state variables but
not much of a buffer of past samples.  The 1024 instructions of Flash
may be a bit of a pinch but should be doable.  It has two timers
(p. 6), which is enough to generate PWM from one while using the other
to measure the charging time.

There's a clock prescaler CLKPR (p. 32) which can divide the master clock by any
power of 2 from 1 to 256, and a separate CKDIV8 "fuse" which is
initially programmed (p. 33), which means the ATTiny2313 runs
at 1 MHz by default (p. 27).  The internal RC oscillator is 8MHz (p. 19) so
you only get 40% of the chip's potential clock speed without a
crystal.  (There are also 4 MHz and 128 kHz internal oscillators,
selectable via CKSEL (p. 27).)

The analog comparator  (p. 168) is specified as having less than 40 mV of offset
voltage and typically less than 10 mV, which is pretty reasonable — it's
better than 0.1% of 5 volts.  And it’s specified as having an input
leakage current of -50 to 50 nA, which is a lot better than I expected. (p. ???)

For sourcing and sinking current it looks like the output impedance is
in the neighborhood of 60Ω (charts on p. 242) or 25Ω when running on
5V (pp. 243–4).  It can do 20 mA per pin.

There's an "input capture unit" that can be configured to latch the
timer value when triggered by the analog comparator on at least timer
1, the 16-bit timer (p. 92).  This seems like a much better option
than using interrupts, which is four clock cycles, minimum, plus
normally a three-cycle jump, and possibly finishing a multi-cycle
instruction that was in progress when the interrupt fired: 7–9 cycles
of latency.  The worst part there is the two cycles of jitter, which
will make hash of any data about fast RC time constants.

There's an implication that there's a clock prescaler specific to
timer 1 (p. 94, where it says it doesn't apply to the optional noise
canceler, which adds four cycles of latency).

Datasheet questions:

- what's the timer precision?
- what's the comparator noise?  hysteresis?
- can we maybe use the pullup to see if the cap is too big?
- what's the input impedance of the pins?  might we have error from that?
- what's the input clamp diode rated for?

STM32/CKS32
-----------

I have two Blue Pills, an STM32 and a CKS32.  These feature a 12-bit
1Msps ADC, 128KiB of Flash, and 20KiB of SRAM, a shitload of pins,
and run at 72MHz.  This
suggests that we ought to be able to do the whole curve tracing
thing.

If we again want to get at least 5 samples before the signal drops
below 100 counts, well, 100 counts is 1/40.96 of full scale, so 3.7
time constants instead of 2.3, so our time constant needs to be at
least 1.35 μs, a hundred times faster than the AVR ADC, attainable at
47 pF with a 29-kΩ resistor.  And if we again want to make sure we get
at least 200 counts of change within the 5-ms window, that's only 4.9%
of full scale, about 0.05006 time constants, so our time constant can
be up to 99.8 ms, which I'll just irresponsibly round to
100 ms — which still requires a 100-Ω high-capacitance-scale resistor.

So even with these more powerful chips, the two resistors are a factor
of 290 apart, which doesn't give you much benefit from all the cute
tricks at the beginning of this document; but now they cover the
center of the range to adequate precision as well.

ATTiny12
--------

The 8-pin ATTiny45 or its larger version the ATTiny85 sell for US$1.50
to US$2 on MercadoLibre here, but there's a vendor that sells the
deprecated ATTiny12 (also 8-pin) for US$0.30 or so, so it'd be
interesting to see if it might be usable for this kind of thing.  It
only has 1 KiB of program memory, enough for 512 instructions, runs at
only 8 MHz (or less in some configurations, like the ATTiny12V-1), and
has no RAM other than its 32 8-bit registers, 64 bytes of EEPROM, and
a 3-level hardware return stack.  It has no ADC, but it does have the
analog comparator — without multiplexed inputs, as in the ATTiny2313,
but with interrupts.  It has a single timer, but without hardware PWM;
you could use it to time the discharge or recharge curve, but you'd
have to do PWM in software, where interrupts would screw it up.  The
internal RC oscillator runs at 1.2 MHz instead of 8 MHz, so you need
to use an external crystal to get higher speed or consistent speed,
which of course eats up two of the 5 GPIO pins.

I feel like this chip would be pretty difficult to get anything done
on due to its extremely limited resources.  Maaybe you could get it to
work for measuring a capacitor, but I'm not sure how.

7-segment LED displays
----------------------

I'm thinking a 4-digit 7-segment LED display is probably sufficient
for a 1%-error meter.  Possible capacitance displays might look like
any of these:

    .001F 100μ 10μ0 1μ00 100n 10n0 1n00 100p 10p0

A "μ" on a 7-segment display can look like a backward 4.
