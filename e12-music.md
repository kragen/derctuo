Resistors, capacitors, and sometimes even inductors are conventionally
manufactured to have a set of “preferred values” in each order of
magnitude: the E3 series, 10 22 47; the E6 series, which intercalates
15 33 68; the E12 series, which additionally intercalates 12 18 29 39
55 82; and longer series.  So it’s common to see a 220-ohm resistor or
a 220-μF capacitor, but you’ll very rarely see a 200-ohm resistor or a
200-μF capacitor.

Despite appearances, these values are fairly evenly spaced — just in
exponential space.  82÷68 ≈ 1.206; 29÷22 ≈ 1.318; 18÷15 = 1.2; and so
on.  The intervals all approximate 10<sup>1/12</sup> ≈ 1.2115.

Component identification — knowing whether this resistor you have is a
2k2, a 22k, or just broken — is a big challenge for salvaging
electronics.  Especially for us old colorblind humans.  Usually it’s
good enough to know which E12 value something is.  Microcontrollers
that can easily distinguish components are easy to come by, but
getting their output into a useful form requires some kind of
[screen](screens.md), and those are comparatively rarer and more
finicky to interface with.

The humans can easily distinguish notes of the 12-tone equal
temperament scale traditionally used for Chinese music (though this is
far easier when presented as intervals rather than as bare notes), and
there’s a pleasing perceptual correspondence between the 12 tones in
an octave and the 12 E12 values in a decade.  And speakers are cheap
and easy; sometimes, as in surface-mount MLCCs using piezoelectric X7R
dielectrics and similar, they’re even included by accident.  Auditory
output also has real advantages for high temporal resolution that the
humans can perceive.

So I propose that a component-identifying smart-tweezer program should
map measured component values to the musical scale in this way and
alternately play a couple of notes through a speaker, separated by the
appropriate interval.  One of the notes can be something like a flute
playing a standard frequency, like 261.62557 Hz for A440 [middle
C](https://en.wikipedia.org/wiki/C_(musical_note)#Frequency) (C₄
except in MIDI), to give a point of reference, while the other plays
after it as a string instrument or something, with each decimal order
of magnitude of component values mapped to one musical octave.  That
is, represent them with a fundamental frequency of
*k* 2<sup>log₁₀ *v*</sup>, where *v* is the value to represent and *k*
is some proportionality constant.

Psychoacoustics
---------------

The humans can easily hear the eight octaves from 32.70 Hz up to
8372 Hz, while the next octave and a half up is mostly audible only to
larval humans; the next octave down from 32.70 Hz to 16.35 Hz is
audible in its pure form only if it is very loud, but if provided with
rich harmonic content, it is clearly comprehensible even when the
fundamental is too low to hear or even not reproducible by the
speakers.  Their hearing sensitivity is [about 1 Hz for complex tones
up to
500 Hz](https://en.wikipedia.org/wiki/Pitch_(music)#Just-noticeable_difference)
and about 0.6% above 1000 Hz; between 1kHz and 2kHz their perception
of frequency is least distorted by amplitude, while [between 2kHz and
5kHz they are most able to detect
sounds](https://en.wikipedia.org/wiki/Equal-loudness_contour).  This
suggests that it’s probably best to stick to the lower octaves as much
as possible, even down below 20 Hz, since their harmonics will
populate the most sensitive regions of hearing more densely.

However, we start to lose relative precision once we go below 500 Hz;
at 500 Hz the frequency precision is about 0.2%, at 250 Hz only about
0.4%, at 100 Hz only about 1%, at 65.41 Hz about 1.5%, and at 20 Hz
only about 5%.  After being transformed through the inverse of the
representation function above, these perceptual imprecisions are
respectively 0.7%, 1.3%, 3.4%, 5.2%, and 14%.  At higher frequencies
errors climb again but not so high.

Resistors, with high resistances at high frequencies
----------------------------------------------------

Resistors generally used by the humans range from 1Ω to 1MΩ, six
orders of magnitude apart, with most being in the 100Ω–100kΩ range,
only three orders of magnitude apart.  Generally low-value resistors
are used for precise measurement (linear conversion between voltage
and current), and current limiting, while high-value resistors are
used for less-precise applications like pullups, pulldowns, capacitor
bleeders, and protection.  This suggests maybe positioning 1Ω around
110 Hz (A₂, A below small C or A above deep C), giving the following
correspondences:

* 10 mΩ: 27.5 Hz, A₀, lowest key on a [normal 88-key
  piano](https://en.wikipedia.org/wiki/Piano_key_frequencies)
* 100 mΩ: 55 Hz, A₁, A below low C
* 1 Ω: 110 Hz, A₂, A below small C
* 10 Ω: 220 Hz, A₃, A below middle C
* 100 Ω: 440 Hz, A₄, A above middle C
* 1 kΩ: 880 Hz, A₅
* 10 kΩ: 1760 Hz, A₆
* 100 kΩ: 3520 Hz, A₇, highest A key on a normal 88-key piano
* 1 MΩ: 7040 Hz, A₈

This of course gives *k* = 110 Hz.

This assignment is a compromise between not “wasting” the lowest
octaves on little-used low resistances that require Kelvin probing to
measure accurately, assigning the best precision to values in the
1–100 Ω range where it often matters the most, and not assigning
megohm values to totally inaudible frequencies.

Resistances above a few megohms might be best represented by some
additional gimmick, like using a different musical instrument.

Capacitances, and a better resistance scale
-------------------------------------------

Capacitances are trickier because they span a wider range; common
capacitors are in the 47 pF to 470 μF range, though up to 22000 μF is
not unheard of — though anything above 1 μF probably isn’t very
precise, because the piezoelectric and electrolytic technologies used
at those higher capacitances aren’t very precise.  (And then there are
supercaps, up to 100 F — that is, 100 000 000 000 pF.)  In the
47 pF–4.7 μF range, though, we have only five orders of magnitude,
which seems quite manageable.

It’s unclear whether to use high frequencies for high capacitances
(like, microfarads) or low capacitances (picofarads).  Arguments in
favor of using them for *low* capacitances include:

1. In real life smaller capacitances do things at higher frequencies.
   0.01 μF is 1 kΩ at about 16 kHz; 100 pF doesn’t drop to 1 kΩ until
   1.6 MHz.  Whether in an LC tank, an RC oscillator, or an RC filter,
   using a smaller capacitor means your frequencies go up.
2. Also, high capacitances tend to be physically larger, and,
   psychologically, larger things should make deeper sounds, while
   tinier things make tinnier sounds.

Arguments in favor of using high frequencies for *high* capacitances
include:

1. Precision is more important for low capacitances, which are largely
   used for tuning things, than for high capacitances, which are more
   used for bypassing things.  If we put 47 pF at 880 Hz so that
   perceptual precision is best around 100 pF, and go down from there,
   then at 0.047 μF we’re already at 110 Hz, and by 4.7 μF we’re at
   27.5 Hz.  What on Earth would we do with 470 μF?  The noise of an
   idling motorcycle without a muffler, at 6.875 Hz?  And we’d be
   wasting a couple of audible octaves that correspond to capacitances
   too small to measure reliably under ordinary conditions, because
   they’re swamped by parasitics.
2. You’ll be able to transfer your numerical ear learning from
   resistance to capacitance more easily.  (“Oh, that C# is 2.2kΩ.
   That means it’s 0.022 μF.”)
3. Higher resistances move RC oscillators and filters to higher
   frequencies too, so what gives?  (Higher resistances move RL
   circuits to lower frequencies, but nobody uses RL circuits.)
4. The difference in response frequencies of capacitors is vastly
   understated by the frequencies you hear.

The rebuttal arguments in favor of using high frequencies for low
capacitances include:

1. Maybe high resistances should be represented as low frequencies
   too, both because they slow down RC circuits and because we think
   of higher resistances as being “larger”, so maybe they should speak
   in deeper voices too?  (Also, *really* high resistances, like 10 MΩ
   and up, tend to be used with high voltages, so they *are*
   physically large, both for power dissipation and creepage
   allowance.  Still, lots of physically large resistors I have here
   are 10-Ω current-sensing shunts or 25-Ω heating elements, not
   multi-megohm high-voltage protection resistors.)

So I propose this scale for capacitances:

* 10 000 μF: 1.71875 Hz, A₋₄, 103 beats per minute, *moderato* or
  *allegretto* musical tempo, hunt-and-peck 17 words per minute typing
  speed; [largest common electrolytic
  capacitor](http://eeshop.unl.edu/standard_values.html)
* 1000 μF: 3.4375 Hz, A₋₃, 206 beats per minute, *prestissimo* musical
  tempo, beginner touch-typist 34 wpm typing speed
* 100 μF: 6.875 Hz, A₋₂, 412½ RPM, too fast to be a musical tempo but
  too slow to be an idling diesel truck engine, respectable typing
  speed of 69 wpm
* 10 μF: 13.75 Hz, A₋₁, Harley engine idling too slow at 825 RPM,
  [near the 800-RPM bottom limit permitted by the stock
  EFI](https://www.v-twinforum.com/threads/idle-rpm-how-low-can-you-go.114338/),
  risking running your starter battery down and also engine damage
  from insufficient oil pressure; 138 wpm, maximum human typing speed
* 1 μF: 27.5 Hz, A₀, lowest key on a [normal 88-key
  piano](https://en.wikipedia.org/wiki/Piano_key_frequencies);
  smallest common electrolytic capacitor; largest common film
  capacitor; largest common ceramic capacitor; microwave oven
  capacitor
* 0.1 μF: 55 Hz, A₁, A below low C; smallest common tantalum capacitor
* 0.01 μF: 110 Hz, A₂, A below small C
* 0.001 μF: 220 Hz, A₃, A below middle C; smallest common film
  capacitor
* 100 pF: 440 Hz, A₄, A above middle C
* 10 pF: 880 Hz, A₅; smallest common ceramic capacitor
* 1 pF: 1760 Hz, A₆, but you probably have parasitic capacitances
  larger than this; the manual for the famous AVR TransistorTester
  says, “Capacitors with value below 25pF are usually not detecte[d].”

So, for example, 120 pF would be about G#₄, 415.30 Hz, one half-step
deeper than A₄, because it’s one step higher on the E12 scale; 150 pF
would be about G₄ (392.00 Hz), and 220 pF would be about F₄
(349.23 Hz).  These are approximate because the E12 scale is
approximate: 120 pF would be more accurately 416.50 Hz, 150 pF more
accurately 389.44 Hz, and 220 pF more accurately 347.03 Hz.  So
150 pF, for example, is flat of G₄ by 11.3
[cents](https://en.wikipedia.org/wiki/Cent_(music)), a difference
audible to trained musicians and possibly somewhat painful, but hard
for the untrained ear to detect, though probably possible in this
region of best sensitivity.  A perfect G₄ would be closer to 146.8 pF,
an error of -2.2% from the nominal value.

Correspondingly, I propose this one for resistances, equating a
microfarad to a megohm, a nanofarad to a kilohm, and a picofarad to an
ohm, as if we were interested in an electrical signal of 160 kHz:

* 10 GΩ: 1.71875 Hz, A₋₄, 103 bpm, *allegretto*
* 1 GΩ: 3.4375 Hz, A₋₃, 206 bpm, *prestissimo*
* 100 MΩ: 6.875 Hz, A₋₂, 412½ RPM
* 10 MΩ: 13.75 Hz, A₋₁, 825 RPM
* 1 MΩ: 27.5 Hz, A₀, lowest key on a [normal 88-key
  piano](https://en.wikipedia.org/wiki/Piano_key_frequencies)
* 100 kΩ: 55 Hz, A₁, A below low C
* 10 kΩ: 110 Hz, A₂, A below small C
* 1 kΩ: 220 Hz, A₃, A below middle C
* 100 Ω: 440 Hz, A₄, A above middle C
* 10 Ω: 880 Hz, A₅
* 1 Ω: 1760 Hz, A₆; at this point you need Kelvin connections for
  precision measurement
* 100 mΩ: 3520 Hz, A₇, highest A key on a normal 88-key piano
* 10 mΩ: 7040 Hz, A₈; at this point you need Kelvin probing for any
  measurement at all

Note that this also solves the problem of what to do with arbitrarily
high resistances, although you have to be careful you aren’t swamping
the whole audio spectrum with harmonics from a spurious detection of a
100-GΩ resistor that’s really leakage through your probe insulation or
something.

It might be reasonable to do, say, a square wave for capacitance and a
repeatedly plucked Karplus–Strong string (or two, slightly offset in
frequency, like a piano) for resistance, so that you can play them at
the same time if you’re doing an ESR measurement.  Using different
envelopes will help the humans hear them as two separate sounds rather
than one sound with a discordant timbre.

Inductors
---------

Now we’re faced with another consistency dilemma: do we represent
large inductances with low pitches or with high pitches?

In favor of low pitches:

1. Large inductances, like large capacitances, are used at low
   frequencies.  A 60-Hz fluorescent light ballast might be an entire
   henry.  A millihenry has a kilohm of reactance at 160 kHz; at 16
   MHz you only need 10 μH to get to 1kΩ.
2. Inductors with large inductances are physically large, so should
   ring deeply, like a church bell, not like a jingle bell.  Also they
   tend to have heavy magnetic cores.
3. It would be numerically consistent with the systems suggested above
   for capacitances and resistances.
4. Of course in LC circuits increasing the inductance lowers the
   frequency.

In favor of high pitches:

1. Steal underwear.
2. ???
3. Profit!
4. In some metaphorical or Steinmetzian sense a large inductance is
   the opposite of a large capacitance.

I think the underwear gnomes lose this one.

If we try to use the same 160-kHz signal frequency to figure out where
to set the equivalence, we get this:

* 10 000 H: 1.71875 Hz, A₋₄, 103 bpm, *allegretto*
* 1000 H: 3.4375 Hz, A₋₃, 206 bpm, *prestissimo*
* 100 H: 6.875 Hz, A₋₂, 412½ RPM; [largest common
  inductor](http://eeshop.unl.edu/standard_values.html)
* 10 H: 13.75 Hz, A₋₁, 825 RPM
* 1 H: 27.5 Hz, A₀, lowest key on a [normal 88-key
  piano](https://en.wikipedia.org/wiki/Piano_key_frequencies),
  fluorescent light ballast inductance
* 100 mH: 55 Hz, A₁, A below low C
* 10 mH: 110 Hz, A₂, A below small C
* 1 mH: 220 Hz, A₃, A below middle C
* 100 μH: 440 Hz, A₄, A above middle C, maybe an air-core coil for an
  RF circuit
* 10 μH: 880 Hz, A₅
* 1 μH: 1760 Hz, A₆
* 100 nH: 3520 Hz, A₇, highest A key on a normal 88-key piano;
  smallest common inductor
* 10 nH: 7040 Hz, A₈, typical axial lead parasitic inductance
* 1 nH: A₉, 14.08 kHz, out of my hearing range, [a wire of 1 mm length
  and 1 mm
  radius](http://users.cecs.anu.edu.au/~Gerard.Borg/engn4545_borg/impedance/inductors.html)

This seems reasonable.

It’s probably worthwhile to use a different instrument again for
inductor detection, perhaps an FM-synthesized bell sound or something.
