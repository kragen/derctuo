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
the kind of galvo rotating mirrors used for laser shows),
later a 1000fps custom-built projector, and a
custom-built 1000fps camera.  Lower-cost DMD devices like the [TI
DLP4710 chip][12] can only manage 120Hz [but still cost US$170][13].

[10]: https://www.ti.com/tool/DLPD4X00KIT
[12]: https://www.ti.com/product/DLP4710
[13]: https://www.digikey.com/en/products/detail/texas-instruments/DLP4710FQL/12604457

Is there nothing we can do to do HCI experimentation with low-latency
user interfaces without shelling out the big bucks?

Seven possibilities occur to me: light pens, Wacom tablets, theremins,
MEMS accelerometers and gyroscopes, inkjet-printer carriage feedback
hardware, acoustic surface triangulation, encoded LEDs and
photodetectors, and impedance tomography.

Light pens
----------

A light pen — described in Ivan Sutherland’s SKETCHPAD dissertation
and [his 1994 talk about SKETCHPAD][22], and he seems to have been
the first to draw with it, but apparently didn’t invent the thing — is a
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
[22]: https://www.youtube.com/watch?v=-sbeghygOt4 "Sketchpad - A Man-Machine Graphical Information System, a talk at the Bay Area Computer History Perspectives lecture series, organized by Peter Nurkse and Jeanie Treichel, recorded on '3/22/94', distributed by Sun Microsystems, Inc., in 1996"

However, because each point on the screen is only scanned once every
33.3 ms, your average latency *in the light pen itself* would be
16.7 ms, while the worst-case latency would be 33.3 ms.  This is an
unpromising place to start.

However, in SKETCHPAD, Sutherland was driving the TX-2’s “scope” with
two registers that were wired up to (10-bit) DACs; so, by writing to
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

I suspect that [boustrophedon horizontal scanning might allow us to use
much higher raster scan rates with the same tube][22], since there’s no
need for an HBI, but then you have to modulate your data to avoid
bright spots where scan lines intersect.  Also, the higher raster scan
rates would place more demand on the light pen’s signal latency and
the phosphor’s rise time in order to achieve the same horizontal
positioning precision.

[22]: https://www.quora.com/In-old-CRT-monitors-why-did-the-electron-beam-shut-off-to-go-back-to-the-beginning-of-the-next-line-rather-than-shoot-the-pixels-from-right-to-left-every-other-line?share=1

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

Light pens with projectors
--------------------------

CRT projectors have similar time-domain behavior, so in theory you
ought to be able to use a light pen by pointing it at the image from a
CRT projector or eidophor in the same way you could use a direct-view
CRT.  However, CRT projectors were never very common and have become
extinct since the 1990s, so it’s hard to find them nowadays.

There’s a specialized kind of CRT projector called a “flying-spot
scanner” which projects a flat light field onto a sheet of paper, then
using the time-domain variation in diffuse reflectance to recover the
original paper image; this is closely analogous to structured-light
3-D scanning, which identifies the parallax to all the points on an
object using the same kind of binary or Gray-code images the Microsoft
researchers used for their touchscreen.  By the 1970s flying-spot
scanners were using specialized low-persistence phosphors to maximize
“flicker” and thus the sharpness of the scanned image.  (Before the
Vidicon tube, such flying-spot illumination was a favored way to take
television images.)

Several times in the past it has occurred to me that you could scan a
time-domain-modulated laser across a surface with mirrors to make a
flying-spot projector.  (Perhaps a planar Kerr cell or Pockels cell
with a voltage gradient along its surface across a resistive surface
film electrode could provide a faster-response alternative to a moving
mirror.)  This approach of course would also work with a light pen in
the same way as the scanning electron beam from a CRT, but without the
problems induced by phosphor persistence and phosphor electron
penetration depth.  Ordinary laser-show galvos are capable of much
faster response than typical CRT vertical deflection yokes.

Ordinary LCD and DMD projectors cannot be used in this way because the
high-resolution time-domain signal is respectively absent or not under
the control of the computer system.  (DMDs control the brightness of
their pixels with PWM at some kilohertz, so they would be able to
transmit tens of kilobits pe second of data if those PWM signals could
be fed in externally.)

A potentially more interesting way to do this would be to use a
separate infrared (or green + infrared) tracking laser to track the
light pen, so that you would never have to move the tracking laser
away from the indicated point, except when you lost it.

Such light pens would probably also usually be indirect pointing
devices, where the user relies on projected feedback from the computer
system to find out where their “hand” is, although with rear
projection (like large multitouch screens use) you could get direct
pointing out of them.

Wacom tablets
-------------

I can’t find good information about the latency of Wacom tablets,
although they’re popular for experimental musical instrument
interfaces.  Apparently the wires in the tablet grid switch between
transmitting and receiving to the pen every 20 μs, though, and
demanding users report higher latency with the USB versions, so I
suspect they’re submillisecond.

Wacom tablets, unlike touchscreens and stylus screens, are normally
indirect pointing devices: what you are looking at is not what you are
pointing to.  This makes the latency demands less demanding, but it
also probably makes people’s performance slower, since they don’t have
a lifetime of experience coordinating their proprioceptive and visual
feedback channels to know how far to move their hand.

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

A theremin is normally also an indirect pointing instrument, but you
could imagine projecting an image with any kind of projector — even an
LCD projector — and using the theremin to detect where your hand was
on the projected image, having calibrated it to the projection setup.
Other kinds of screens (LCD screens, OLED screens) would probably
overwhelm the theremin signal with electrostatic noise.

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
possibly the signal is super noisy, and which isn’t really documented
in the datasheet except to say that it’s 5–250 Hz.  The specs say it
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
They would of course also be indirect pointing devices.

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

Inkjet-printer linear optical encoders might be used as direct or
indirect pointing devices.

Acoustic surface triangulation
------------------------------

Last year, in the “Audio Tablet” note in Dercuano, I wrote about using
the sound conducted through a surface between a stylus and two or more
reference transducers to detect the distance along the surface to the
stylus.  The idea is that the audio lag time along the paths through
the surface tells you what the distance is; then it’s just a matter of
damping the waves when they reach the edge of the surface so they
don’t rebound and give you multipath.  Typical sound speeds through
solids are kilometers per second, which is millimeters per
microsecond, so a microsecond or so is about the right level of
precision on the lag, and so the acoustic signal needs to be a few
MHz, which won’t propagate far through air but has no trouble with
most solids.  You can use pulses, noise signals, or perhaps even just
the scratching of the stylus on the surface, though in that case it
might lack the requisite MHz-frequency components.

(If you’re using lower-frequency acoustic signals, you might not be
able to use the time lag, but be forced to use the attenuation, a
technique I learned from David H. “n2” Christensen, [RIP, PBUH][25].)

[25]: https://electro.david.promo/the-last-goodbye/

This has the inherent latency of the audio propagation time, which
might be up to a millisecond or so depending on how many transducers
you’re using, plus several microseconds to measure the correlation.

This technique should still work if the surface you’re using is a
screen displaying an image, whether from front projection, rear
projection, or an LCD.

Encoded LEDs and photodetectors
-------------------------------

All common LEDs, except the white ones, have submicrosecond response
times, so you can modulate them at megabits per second; so do all
common photodiodes, although some phototransistors are a bit slower.
If you’re modulating an LED with some random bit sequence, or even
just a sine wave, at hundreds of kilobits per second, you should be
able to run a correlation with the signal from such a photodetector
(at zero lag, if they’re within a meter or two) to measure the
strength of the coupling between the LED and the photodetector.  The
correlation can be done with a simple analog chopper circuit, or
digitally to get a window shape closer to the ideal boxcar; if the
modulating signal is a simple sine wave, you can even use a simple
tuned filter.  Since LEDs and photodetectors are somewhat directional,
this coupling strength is a function not only of the distance between
the devices but also their relative angles, but (unlike with a laser
pointer) it typically doesn’t drop off to near-zero until the LED or
the photodetector are pointed nearly 90° off-axis.

If you have two such encoded LEDs mounted on an object at different
angles, carrying different signals, this gives you two degrees of
freedom, which allows you to separate the factor of the coupling due
to the orientation of the object from a factor that combines the
distance to the object and its closeness to the photodetector’s
optical axis.  Adding two additional photodetectors gives you a total
of six coupling constants, one between each photodetector-LED pair,
which in theory might be enough to measure the position and
orientation of the object in all six degrees of freedom; I suspect you
might actually need three LEDs on the object to disambiguate
orientations reliably.

Moreover, these three photodetectors are in theory sufficient for any
number of objects as long as their optical signals are uncorrelated
and the photodetectors don’t saturate, or don’t saturate much.

Measuring the relevant correlations to the necessary degree of
precision should in theory take much less than a millisecond when
using signals modulated at hundreds of kHz which are perfectly
uncorrelated over millisecond timescales.  250kbps random bitstreams
have a bit per 4 μs, so surely over 100 μs their Hamming distance will
be quite large.  (An even simpler alternative is that each LED could
simply transmit its callsign over and over, but I suspect that will
tend to perform worse.)

You can use this for either a direct pointing device positioned on a
screen, perhaps for a ring worn on the hand, as long as the
photodetectors are looking down at the screen from known positions on
the same side as the user, or an indirect pointing device in an
arbitrary place in space.

I think some virtual-reality gear from the 1990s used this approach
with an ultrasonic signal rather than an optical one, thus enabling it
to use the 343-μm-per-μs speed of sound in air to get distance
information.

Impedance tomography
--------------------

I’ve seen some recent papers using “impedance tomography” over a
resistive surface under a dielectric layer to detect finger touches on
the dielectric layer; a series of eight or so electrodes around the
edge are alternately stimulated to measure the pairwise impedance
between all pairs of electrodes, which changes when a finger
capacitively couples some of the surface to ground, which allows you
to approximate the finger position.  In theory this could be done very
quickly, but the papers I’ve seen didn’t achieve submillisecond
latency, so maybe there’s some obstacle such as high noise.  I suspect
this is probably unavoidably an indirect pointing method.

Thanks
------

Thanks to Brandon Moore and Greg Sittler for the discussion this note
arose from.
