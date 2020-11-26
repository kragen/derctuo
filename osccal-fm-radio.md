Hmm, I just realized that the AVR family OSCCAL oscillator calibration
register can adjust their internal oscillator frequency, normally 8MHz
or 16MHz, by fine degrees.  The 12th harmonic of an 8MHz square wave
would be 96MHz, which is within the FM radio band
(87.5 MHz–108.0 MHz).  The maximum permitted deviation from a nominal
carrier frequency is ±75 kHz, which would be ±780 ppm at 96 MHz,
±860 ppm at 87.5 MHz, and ±690 ppm at 108 MHz.  So, if "fine degrees"
is about 1900 ppm or less (0.2%), then I ought to be able to transmit
at least impulses and square waves over FM radio in this way.  In
fact, since FM radio stations have about 100 kHz of bandwidth after
demodulation, that would be sufficient to generate PWM audio by
spending different percentages of the time at each different wave.

In the ATTiny2313 OSCCAL is 7 bits, selecting one of 128 frequencies
in order to calibrate down from a nominal ±10%, so the situation
doesn't look that great.  Maybe a better plan is the thing I'd wanted
to try previously: put a resistor on the AVR's V<sub>CC</sub> pin and
modulate its *current consumption* in order to affect its operating
frequency an therefore the frequency of the waves generated.

Also there's an ominous note in the datasheet saying that the
processor needs to remain in RESET while OSCCAL is being written.

On p. 200 the datasheet says that with user calibration the oscillator
can range from 7.3–9.1 MHz, with ±2% error at the given voltage and
temperature.  On p. 230 we have a chart suggesting that OSCCAL is
capable of moving the frequency anywhere from 3 MHz or so up to
12 MHz!  It looks like an approximate exponential, too, suggesting
that each step of OSCCAL is about 1.1% of frequency variation.  So
that idea probably won't work, unless there are two adjacent OSCCAL
values that are anomalously close.

