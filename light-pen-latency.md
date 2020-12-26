There’s a lot of recent HCI research on the impact of UI latency on
computer usability.  The picture is abysmal: [keyboard latency is
70–200 ms][0] and touchscreen latency is 70–250; even musical
performance systems like the [Reactable][1] suffer from 100–300 ms
input lag (I counted frames in that video); the experimental tangible
programming system at [Dynamicland][2] is kneecapped by a latency in
the 600–2500 ms range.  Meanwhile, [people can tell the difference
between 1 ms and 2 ms of dragging lag][3] in user tests with a
custom-built low-latency stylus tracking system, or [detect 10 ms of
latency in a different experiment][5]; [10 ms of latency at dragging
or drawing is extremely obvious][6]; performance in the simplest
touchscreen tasks is [hampered by input latency over 25–50 ms][4]; and
with 1–3 ms of latency, [projection-mapping can convincingly add
textures to moving and flexing objects][7], [even when some of the
light leaks around the edges][8] or [the objects ripple in the wind][9], though
[commercially available real-time projection mapping systems let the
projection mapping visibly slide][11] around over the moving surface
due to their higher latency, thus failing to achieve the desired
illusion.

[0]: https://danluu.com/input-lag/
[1]: https://www.youtube.com/watch?v=Bg1SPdWEjuo "A Reactable performance from 2019"
[2]: https://www.youtube.com/watch?v=HPvRPOeKUx0 "Cayley diagrams"
[3]: https://webdocs.cs.ualberta.ca/~wfb/publications/C-2014-SIGCHI-Latency.pdf
[4]: https://www.youtube.com/watch?v=A2yGdqGUpdk "Ishikawa Group’s video figure on Allowable Limits of Latencies in Delay Control Visual Feedback System"
[5]: https://www.tactuallabs.com/papers/howMuchFasterIsFastEnoughCHI15.pdf
[6]: https://www.youtube.com/watch?v=vOvQCPLkPt4 "Paul Dietz at MSR talking about latency and demonstrating 1-ms latency"
[7]: https://www.youtube.com/watch?v=QDppJ9NWtaE "Ishikawa Watanabe Lab DynaFlash v2 demo video"
[8]: https://www.youtube.com/watch?v=XEseo-orRDI "Ishikawa Senoo Lab VarioLight demo video"
[9]: https://www.youtube.com/watch?v=-bh1MHuA5jU "Ishikawa Watanabe Lab video figure for ‘Dynamic projection mapping onto deforming non-rigid surface’"
[11]: https://www.youtube.com/watch?v=XkXrLZmnQ_M "Panasonic real-time tracking and projection mapping marketing video"

The Microsoft system used above used a custom-built “high-performance
stylus system” using Gray-code-modulated light patterns projected at
24kHz with a [TI DLP DMD “projector development kit” which costs
US$1800][10]; the Ishikawa .\* Lab research used both a projector with
a custom-built 1000fps tracking mirror setup (using what looks like
the kind of galvo rotating mirrors used for laser shows) and a
custom-built 1000fps camera.  Lower-cost DMD devices like the [TI
DLP4710 chip][12] can only manage 120Hz [but still cost US$170][13].

[10]: https://www.ti.com/tool/DLPD4X00KIT
[12]: https://www.ti.com/product/DLP4710
[13]: https://www.digikey.com/en/products/detail/texas-instruments/DLP4710FQL/12604457

Is there nothing we can do to do HCI experimentation with low-latency
user interfaces without shelling out the big bucks?

Five possibilities occur to me: light pens, Wacom tablets, theremins,
MEMS accelerometers and gyroscopes, and inkjet-printer carriage
feedback hardware.

Light pens
----------

A light pen — described in Ivan Sutherland’s SKETCHPAD dissertation,
but I forget if he invented the thing or not — is a
high-temporal-resolution light sensor in a tube.  You point it at a
CRT screen, and it detects the time when the electron beam illuminates
the point the pen is pointed at on the screen.  A [6-ns photodiode
covering the whole visible spectrum will run you 26¢ in quantity 10 at
Digi-Key][14] or [63¢ for a bigger 5-ns one][15], although some common
photodiodes are as slow as [50 ns][18] or 100 ns.

An [NTSC TV set runs 29.97 full interlaced frames per second][16] at
525 lines per frame (486 visible), so the electron beam in an NTSC CRT
takes 63.6 μs to sweep across each line, including the [10.9 μs
horizontal blanking interval][17].  Typically about a quarter of the
screen is illuminated at any given time, as viewed through a
high-speed camera, about 4–5 ms (a fourth of a 16.7-ms field), because
that’s how long the phosphors take to fade
(maybe [200 ns P22B][21], 850 μs P22R, 35 μs P22G).
So the brightness at any
given point on the screen rises sharply once or twice per 33.3-ms
frame, with a rise time limited mostly by the focus of the electron
beam (or, at high beam energies, the contagion of cathodoluminescence
through the phosphor, or sometimes the [rise time of fluorescence][19] in the
phosphor, which is on the order of 10 ns),
and then fades away exponentially to zero over 4–5 ms.  Since
each scan line is 52.7 μs, not counting the HBI, 100 ns is a 527th of
a scan line, and 5 ns is a 10,540th of a scan line.  So any old
photodiode would work to get near-single-pixel precision on a light pen
driven by a regular NTSC raster.  (High-quality analog CRTs could
reproduce 400 dark-light cycles across a scan line, so we can consider
them to have about 800 pixels per scan line, but NTSC was more
limited.  The whole [NTSC broadcast signal was only 6 MHz in bandwidth][20],
which is 200 kilocycles or 400 kilopixels per 29.97-Hz frame; split
across 525 lines, that’s only 762 pixels per line, only 52.7/63.6 =
632 of which were outside the HBI.)

[14]: https://www.digikey.com/en/products/detail/everlight-electronics-co-ltd/PD204-6C/2675631
[15]: https://www.digikey.com/en/products/detail/osram-opto-semiconductors-inc/SFH-213/607286
[16]: https://en.wikipedia.org/wiki/NTSC#Lines_and_refresh_rate
[17]: https://en.wikipedia.org/wiki/Horizontal_blanking_interval
[18]: https://www.digikey.com/en/products/detail/everlight-electronics-co-ltd/PD70-01C-TR7/2675864
[19]: https://apps.dtic.mil/dtic/tr/fulltext/u2/a221095.pdf "USAARL Report No. 83-5, Analysis of image smear in CRT displays due to scan rate and phosphor persistence, Clarence E. Rash, October 1982"
[20]: https://en.wikipedia.org/wiki/NTSC#Transmission_modulation_method
[21]: https://en.wikipedia.org/wiki/Phosphor

However, because each point on the screen is only scanned once every
33.3 ms, your average latency *in the light pen itself* would be
16.7 ms, while the worst-case latency would be 33.3 ms.  This is an
unpromising place to start.

However, in SKETCHPAD, Sutherland was driving the TX-2’s “scope” with
two registers that were wired up to (12-bit?) DACs; so, by writing to
these registers, he could position the electron beam at any position
on the screen immediately.  To track the light pen, he drew a
crosshair around the point where the pen was believed to be pointing,
and by detecting which part of the crosshair was stimulating the pen’s
photodetector (by way of their timing), he could detect when the pen
had moved somewhat.  The TX-2 was far too small and too slow to handle
a raster scan at any kind of reasonable resolution.

Light pens on NTSC CRTs
-----------------------

I find PAL CRT TVs discarded on the sidewalk on a regular basis; some
are NTSC-capable as well, and PAL is broadly similar to NTSC.  Usually
the deflection coils have been recycled before I get to them,
destroying the CRT.  I’m told that in the US the going rate for a CRT
TV is US$25 — paid to the person who hauls it away.

You could rip out the sync and scanning circuitry from a regular NTSC
CRT TV and drive its deflection coils from custom circuitry so as to
revisit the area around your light pen more often.  This won’t be
quite as simple as the electrostatic deflection used in oscilloscope
tubes and the TX-2 “scope” — magnetic deflection coils have
substantial inductance, and the vertical scan coil in particular isn’t
guaranteed to be able to cope with more than a few hundred Hz of
bandwidth; a 60-Hz vertical scan with a fast vertical retrace.  But
you probably don’t need it to.  (The horizontal deflection coil
normally runs at 15.73 kHz, so it should be fine down to deep
sub-millisecond latency.)

The particular form of bandwidth limiting is that it the electron
beam’s vertical position is determined by the coil’s current, and the
voltage aplied to the coil is proportional to the position’s
*derivative*, and you can only apply so much voltage before something
breaks.  So, in theory, there’s no difficulty with instantly starting
and stopping the vertical scanning motion, though some parasitic
capacitances across the coil might cause that until you counteract
them; what you can’t do is move the electron beam quickly vertically,
like probably at more than about one screen height per millisecond.
The NTSC vertical blanking interval is 16.67 ms × (525 - 486)/525 ≈
1.24 ms, but maybe you’ll get lucky and get a faster tube.

So you could spend some of the time doing a semi-normal raster scan
over the whole display, but periodically, like every 4 ms, take a
break from drawing the normal raster image, jump down to where you
expect the cursor to be, draw a crosshair or whatever to see if it
generates a pulse in the photodiode, and then start drawing raster
again.  Once every field (you could perhaps increase the interlacing
from NTSC’s two fields up to three or four fields) you can do this for
free; if the cursor isn’t too close to the top or bottom of the
screen, you can start drawing the raster starting *from* the cursor,
moving up or down according to where pixels need painting.  So if the
cursor is in the middle of the screen, for example, you can paint each
field in two halves, one starting from the cursor and going up, the
other starting from the cursor and going down.  This might save you
the return fare from your round-trip ticket.  (I’m not convinced it
will actually help, though.)

Sometimes, though, especially if the cursor is close to the top or
bottom of the screen, you’ll have to spend the time to jump up or down
to the cursor, then jump back to what you were drawing.

So one scheme is:

- 15.73 kHz horizontal raster scan most of the time.
- 29.97 Hz raster frame rate (33.37 ms per frame).
- 4:1 interlacing, so each field is 8.34 ms, implying a 120 Hz vertical
  retrace frequency, which is probably feasible but may be challenging.
- Three cursor probes per field: one when it happens to reach the
  cursor and thus costs no travel time; one from a worst-case vertical
  distance of ⅓ field away from the cursor and thus 0.41 ms each way,
  0.83 ms total; and one from a worst-case vertical distance of ⅔
  fields away from the cursor and thus 0.83 ms each way, 1.7 ms total.
  So in the worst case we spend 2.48 ms of our 8.34 ms field waiting
  for partial vertical retraces to go to and from the cursor position
  to probe it.
- Boustrophedon vertical scanning, so we don’t have to waste any other
  time on vertical retrace; to keep the scan lines parallel, when
  drawing upwards, we scan right to left, but when drawing downwards,
  left to right.
- The cursor probing proper is restricted to 5% of the height and
  width of the screen (24 scan lines, each scanned horizontally 5% of
  the screen width) so it takes about as much time as drawing a single
  scan line, 60 μs or so.  This can also be used to rapidly update
  images around the cursor to reduce visual feedback latency.
- So, out of the 33.37 ms per frame, we spend 0.72 ms probing for the
  cursor 12 times, 9.92 moving the beam vertically to and from where
  we’re tracking the cursor, leaving 22.7 ms to draw pixels.  This
  gives us 357 scan lines of actual data, which is respectably close
  to the 486 visible scan lines of normal NTSC.

I suspect that boustrophedon horizontal scanning might allow us to use
much higher raster scan rates with the same tube, since there’s no
need for an HBI, but then you have to modulate your data to avoid
bright spots where scan lines intersect.  Also, the higher raster scan
rates would place more demand on the light pen’s signal latency and
the phosphor’s rise time in order to achieve the same horizontal
positioning precision.

This would give us 2.8 ms worst case input latency, 1.4 ms average,
and almost a quarter of a megapixel.  “VGA” resolution, you might say.
This is capable of supporting some HCI experiments with about two
orders of magnitude better latency than commonly deployed user
interface hardware.

A less demanding way to use the device would be to only draw things in
a narrow band, like ½ of the screen height, around the cursor.  Maybe
you could occasionally sneak away to draw something on the outer parts
of the display, or maybe you could just leave letterboxing black
strips at the top and bottom of the screen.

Light pens with VGA CRTs
------------------------

I also regularly find discarded computer CRT monitors on the sidewalk,
also usually with the deflection yokes having been already recycled by
scavengers.  These are similar to NTSC TVs, but typically support at
least 1024×768 pixels at 72 Hz, implying a horizontal deflection
frequency of at least 55 kHz.

This would probably be a bit superior to an NTSC TV, but maybe not as
much as you might hope, since the vertical slew rate is still the
limiting factor on input latency.

Wacom tablets
-------------

I can’t find good information about the latency of Wacom tablets,
although they’re popular for experimental musical instrument
interfaces.  Apparently the wires in the tablet grid switch between
transmitting and receiving to the pen every 20 μs, though, and
demanding users report higher latency with the USB versions, so I
suspect they’re submillisecond.

Theremins
---------

A theremin, invented in 1920, capacitively senses the distance to the
user’s hand by inducing a small frequency shift in an RF oscillator of
a few hundred kHz, whose resonator is a tank circuit including the
user as part of the capacitor.  A second RF oscillator is calibrated
to have nearly the same frequency; by heterodyning the two, an audio
difference frequency is produced.  Thus a difference that is very
small in relative terms — the difference between 100 Hz and 2000 Hz is
only 1900 Hz, and on a signal resonating around 400 kHz, that’s only a
difference of about 0.25%, [produced by a difference of about 0.01
pF][theremin WP].  But this difference can be easily detected.

[theremin WP]: https://en.wikipedia.org/wiki/Theremin#Operating_principles

If I understand correctly, the theremin’s memory, and thus its maximum
possible response latency, is around a millisecond.  If you want to
use the theremin principle for low-latency gesture detection, though,
you probably want to use slightly higher beat frequencies, or directly
measure the frequency of the oscillations rather than heterodyning
anything, because measuring the frequency of a 440-Hz distorted sine
wave in less than a millisecond is, if not impossible, at least
unnecessarily difficult.

MEMS accelerometers and “gyroscopes”
------------------------------------

Every new cellphone or tablet computer has one of these MEMS
accelerometer chips.  I think the [ADXL350] is typical of the genre:
3×4 mm, 3200 samples per second, 2 milligee resolution, typically a
few tens of milligees offset error.  A pointing device containing such
a chip could in theory give you hand orientation and movement feedback
at 300-μs latency, but of course a cellphone can’t manage anything
like such low latencies; from Digi-Key these devices cost US$7.38 in
quantity 10.

[ADXL350]: https://www.digikey.com/en/products/detail/analog-devices-inc/ADXL350BCEZ-RL7/3672596

There are also similar “gyroscope” chips that directly detect
rotation, as well as “IMU” chips combining both; rotation might
actually be a more amenable input modality for pointing at things.
The [TDK IAM-20380] MEMS gyro costs US$10.32 in quantity 10 and gives
you 16-bit readouts on rotation speed around three axes, with 16.4 to
131 counts per (degree per second) depending on which range you have
set, about ±2 degrees per second of offset, and 8000-sample-per-second
output — but with a built-in low-pass filter, which suggests that
possibly the signal is super noisy, and which isn't really documented
in the datasheet except to say that it's 5–250 Hz.  The specs say it
has 0.010 dps/√Hz noise spectral density, which suggests that at
500 Hz (thus 1000 samples/second) you’d expect about 0.2 dps of noise,
which sounds pretty tolerable for a low-latency pointing device.

[TDK IAM-20380]: https://www.digikey.com/en/products/detail/tdk-invensense/IAM-20380/9953664

Even the 2 ms latency implied by the 250 ms low-pass filter setting
would be a vast improvement over conventional I/O devices.

A cheaper option is the [ST L2G2ISTR], which costs US$2.54 in quantity
10 and has only two axes; it has 131–262 counts per (degree per
second) and 9090 samples per second.  Its target market is “optical
image stabilization”, so presumably digital cameras use it to tilt
their mirrors around, and high sample rates and low latency are
obviously a *sine qua non* there.  It has a worse offset rating of
±5°/s and a better noise density rate of 0.006°/s/√Hz.  It also has a
built-in LPF, which goes up to 350 Hz, but it can be disabled with the
`LPF_D` bit in the `CTRL_REG3` register; it claims that this LPF
imposes 7° of phase delay at 20 Hz, which is about a millisecond of
latency.

[ST L2G2ISTR]: https://www.digikey.com/en/products/detail/stmicroelectronics/L2G2ISTR/5268014

I suspect that such chips can be scavenged from discarded cellphones.

Inkjet printer feedback strips
------------------------------

Inkjet printer carriages have precise positional feedback with about
20-μm precision, typically using an optical quadrature encoder made of
four differential slit photointerruptors with some integrated
comparators and a strip of transparent plastic with black stripes
printed on it; typically these operate at about 200 mm/s in normal
printer operation, suggesting about a 10kHz encoder transition rate.
If you could arrange your input device to move such a carriage, you
could decode the quadrature signal and probably get submillisecond
latency out of that hardware.

Thanks
------

Thanks to Brandon Moore and Greg Sittler for the discussion this note
arose from.
