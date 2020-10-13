I mentioned the CCD oscilloscope idea from Dercuano on ##electronics,
and Stipa pointed out that the whole laser mirror thing was far more
complex than needed.  You can just use a VGA monitor!

In more detail, the problem to be solved is that of building an
oscilloscope out of discarded junk, for ghettobotical purposes.  With
an oscilloscope, designing, debugging, and characterizing electronics
becomes enormously easier; some old vacuum-tube oscilloscopes had a
rolloff (-3dB point) of 10MHz, but the standard basic analog
oscilloscope for decades was 20MHz.  So expedients like wiring your
signal source to your sound card through a resistor and some limiter
diodes are grossly insufficient: your sound card is 20kHz, three
orders of magnitude crappier.

Analog oscilloscopes achieved these bandwidths by using a cathode-ray
tube; the beam's Y deflection was what you observed on the screen.  So
you might think that you could salvage an old TV and use its CRT, but
TV CRT beams are magnetically deflected, while oscilloscope CRT beams
are electrostatically deflected, allowing deflection frequencies two
or three orders of magnitude higher at reasonable voltages.  TV CRTs
are not going to be useful for that; they're designed for horizontal
scanning with a 15.734-kHz sawtooth wave (for NTSC; PAL and SECAM vary
slightly.)

However, the beam *intensity* is modulated at higher frequencies, up
to about 5 MHz in the case of NTSC, PAL, or SECAM TV; more promisingly
still, tens of MHz in the case of computer monitors, whose VGA
connectors still accept analog waveforms for red, green, and blue.  A
monitor running at 1600×1200 at 85 Hz, which was high-end in the 1990s
but quite likely junk today if it's a CRT, is drawing 163 million
pixels a second, so it can "sample" signals up to 80 MHz or so.  A
lower-end 60Hz 1024×768 monitor is only drawing 47 million pixels per
second, but that's still enough for more than the 20 MHz for a basic
oscilloscope.  Pixel brightness is not linear in signal voltage, being
transformed by the "gamma curve", but it's not outrageously nonlinear.
And an RGB VGA monitor, like nearly all of them, will "sample" three
channels at once, which is very respectable indeed.

You also need electronics to generate sync pulses, of course.

So how do you get the signal off the screen?  With a camera, of
course.  Many-megapixel cameras are now common on cellphones, and they
are fast enough, high enough resolution, and clean enough (with good
signal-to-noise ratios) that you can use them capture individual
pixels from an individual video frame off a monitor.

(My previous proposal used a laser diode and two spinning mirrors to
scan the laser beam across a reflective screen — certainly doable with
discarded materials, but substantially more difficult.)

Once you have that data, it's a Simple Matter of Programming to find
the individual scan lines, compensate for lighting and viewing-angle
variability, undo the gamma-curve transformation, look for triggers in
the signal stream, and draw a waveform with the resulting data.

There are some limitations of this method.  All data is lost during
the horizontal blanking interval and vertical blanking interval,
unless you are driving three separate monitors out of phase to avoid
this.  (Two monitors is not enough to eliminate these dropouts because
the VBI of each monitor will inevitably contain many HBIs of the
other.)  There are cases where this matters: where you're watching for
a single-shot event and you really need a lot of data on both sides of
it, for example.  Calibration may drift, since slight brightness
changes don't affect the normal use of monitors; occasionally
displaying a calibration frame or two instead of the input signal may
help with this.

Aside from its use as electronics test equipment, this approach should
work well for software-defined radio, in which case it is probably
possible to use analog-domain downconversion and frequency filtering
to smear the signals of interest around in the time domain enough that
the HBI and VBI are less problematic.

