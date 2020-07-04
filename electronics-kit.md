When I was a kid I had a Radio Shack “Science Fair 200-in-1 electronic
project kit”, similar to the 150-in-1 kit Fran Blanche recently talked
about on her show.  It was designed in 1981.  I don’t know if I built
10 circuits with it or 100, but probably somewhere in that range.

According to the manual, it contained:

* a bunch of wires, about 80 in all;
* 20 resistors (one 100Ω, two 330Ω, three 470Ω, four 1kΩ, one 2.2kΩ,
  two 4.7kΩ, and one each 10kΩ, 22kΩ, 33kΩ, 47kΩ, 100kΩ, 220kΩ, and
  470kΩ);
* 3 diodes (one germanium 1N60, the others 1N4143);
* 4 transistors (two 2SC945 NPN, two 2SA733 PNP, all Si);
* 10 capacitors (one each 100pF, 0.001μF, 0.005μF, 0.01μF, 0.05μF,
  0.1μF, 3.3μF, 10μF, and two of 100μF; mostly ceramic but with the
  four largest electrolytic);
* a 9V 500Ω relay;
* a 265pF variable capacitor for radio tuning;
* a 250μA 650Ω galvanometer;
* a “control and power switch” (a 50kΩ pot with a switch at one end);
* six standalone LEDs;
* a 3-volt incandescent lamp;
* a single-digit 7-segment LED display with cathode resistors already
  connected;
* a 350 μH ferrite loopstick antenna with two coils on it, one
  center-tapped;
* an SPDT switch;
* an 8Ω dynamic speaker;
* 2 small transformers, one suitable for driving the speaker from a
  signal in the neighborhood of 5V (“900CT: 8 ohm”), the other “input”
  (“4K CT: 2K”), both with a center-tap on the high-voltage side;
* a piezo earphone;
* a 7400 quad-NAND chip;
* a 7476 dual J-K flip-flop;
* a KC-4SA cadmium-sulfide light-dependent resistor;
* an enclosure for six AA batteries (and no plug-in power supply);
* a momentary-contact button “key”;
* two screw post terminals; and, perhaps most importantly,
* the instruction manual, including instructions for 200 circuits you
  could build.

Not counting the wires, that’s 62 components, most of which cost a
cent or so nowadays, although I think at the time the kit was more
like US$100.  The components were mounted on brightly printed
cardboard with some extension springs mounted around them; these
served to grab the stranded copper wire when you fingered them
sideways.  I don’t know what the advantage of this method was over
jumper wires in a standard breadboard, except that I guess each
component terminal has a unique identifying number, so the wiring
instructions in the manual could say things like “1-81-84,
2-41-49-55-176, 26-44-46,...”, and you could be reasonably sure you’d
hooked it up correctly.

The designs of the circuits are pretty interesting in that they are
adapted to the very minimal resources and poor tolerances available in
the kit; they include a few different single-transistor oscillators,
for example.  (I think they’re Hartley oscillators, often using the
center tap on the audio output transformer for their tapped coil, but
I’m not sure I understand them.)

The circuits include various kinds of AM radio transmitters and
receivers, various kinds of audio oscillators, games that control
audio oscillators etc. with light, a “strobe light” with an LED,
push-pull amplifiers, RTL and DTL logic gates, a “door alarm”, random
number generators, a divide-by-4 counter with decoded output, a VCO, a
voltmeter, an ohmmeter, and so on.  Many of the circuits use the
speaker or piezo earphone as microphones.

It’s been 39 years since it was designed, and a few of the components
are obsolete (TTL logic, germanium diodes, and variable capacitors)
while others are harder to find (CdS cells, piezo earphones, galvos,
relays, incandescent bulbs).  And nowadays, if you were designing
something similar to build out of new parts, you might take advantage
of some of the parts that are cheaper and more robust than they were
then: power MOSFETs, op-amps (maybe LM324s, TLC272s, and as Viper-7
suggests (see file `notes/jellybeans.html` in Dercuano), TL084s for
JFET input), Schottky diodes, Darlington arrays like the ULN2003,
zeners, colored LEDs, some 555s, phototransistors, but especially and
above all else, microcontrollers.  If you’re going to have discrete
logic circuits, make them CMOS.

Toward a ghettobotics version
-----------------------------

If we’re limited to parts we can salvage from discarded equipment,
what could we patch together?

The easiest way to get wire is from discarded wire, especially power
cords, but sometimes also things like telephone line and coax.

Batteries are right out, but there are lots of perfectly capable AC
power supplies out there.  Surprisingly, the power supply often is not
the first thing that breaks; sometimes it’s the supply chain.

LEDs, silicon signal diodes, resistors, capacitors, buttons, and
switches are abundant, and optointerruptors are found at times; most
power supplies also contain transformers, inductors, silicon PN power
diodes, and Schottky diodes.  Speakers are reasonably common.  Crystal
resonators are also quite common (this VCR has nine of them),
potentially permitting very high precision timing measurements.
Potentiometers with knobs attached do occur occasionally, but trimpots
are enormously more common.

Even this 12-watt LED lightbulb that burned out the other day in the
bathroom has a little power-supply board in it containing two
resistors, an MLCC capacitor, a diode, two electrolytic capacitors,
and a transformer (a center-tapped coil, really), plus a couple of
chips (one of which may be a bridge rectifier), plus 14 bright LEDs in
series, two of which are burned out.  Perhaps the power supply works
fine and it was just the LEDs that overheated, in which case I have a
non-isolated power supply the size of my fingertip designed to supply
some 56 volts, 300 mA, from 240VAC.  Or perhaps it would be more
useful in pieces.

Transistors are a little messier.  The VCR, from 1996, has apparently
several hundred of them, but apart from half a dozen power transistors
in its power supply, they’re mostly tiny surface-mount components.  I
more often find BJTs than MOSFETs, but in this case I haven’t looked
them up yet.

Inductors are a sufficiently expensive component that the 200-in-1 kit
didn’t have *any* except as part of its transformers and antenna.  But
they are straightforward to make by hand from wire, especially for low
inductances, or to salvage from discarded equipment.

Connectors are another tricky question.  The 200-in-1 kit had only 62
electronic components — including post lugs to attach wires to — but
some 80 wires and 176 springs.  The dude from Espacio de César
demonstrated rigging up a solderless breadboard out of DIP sockets
from old circuit boards — snip the two sides off and you have two rows
of 2.54-mm-spaced socket holes you can plug pins into.  Other
connectors, such as DIMM slots or CPU sockets, may also work for this.
Through-hole components are easy to slot into those, as long as the
leads aren’t too short, but surface-mount components need to have pins
added to them.

Consumer electronics are by and large full of single-sided PCBs, which
are full of jumper wires, which can be pressed into service as pins in
a pinch, but a better alternative when possible is to rip apart male
Molex-style connectos.

Connectors are also very valuable for a different reason: they permit
modularity, and if you’re generating, say, an audio or video signal,
you can use them to connect it to something external.

7-segment LED displays can still be found in things like discarded
clock radios, but a better option may be to build them out of
now-abundant LEDs and commonplace non-electronic materials like paper
and aluminum foil.  

CdS cells are virtually unheard of in the last decades, but
phototransistors are ubiquitous, though most often infrared, often
with shielding.  LEDs can sometimes serve as photodiodes, too,
although they are poorly characterized for this use.

A soldering iron and soldering flux may be difficult to improvise.

The circuit cookbook probably can’t be as cut-and-dried as the Radio
Shack cookbook was, because the available components will be more
variable.

### Bootstrapping sequence ###

You need to start from basic tools.  First you need a power supply
with voltage in a reasonable range.  But you need to be able to detect
that its voltage *is* in a reasonable range.  How do you do that
without a multimeter?

#### A voltage detector from four LEDs and two resistors ####

A white illumination LED from a lightbulb can probably dissipate a
whole watt, no problem, which is 300 mA or so, and it will probably
light up visibly with any current above 0.1 mA.  You probably want a
couple of separate measuring instruments here, made of two such LEDs
in antiparallel in series with a resistor: one to ensure that the
voltage is not outrageously high, one to verify that there is some
useful voltage.

The not-outrageously-high detector uses a resistor in the 100kΩ–1MΩ
range, which should illuminate the LED and heat up the resistor
noticeably, but probably not burn up, if placed across a circuit
carrying hundreds of volts.  Still, you want to make sure you’re using
a through-hole kind of resistor for this to handle the heat, not a
surface-mount.  At 100V and 1MΩ you get 100μA, which should be visible
on the LED, if barely.  If both LEDs light up, you know it’s AC.

The some-useful-voltage detector is used after you’ve established that
the circuit doesn’t have 100V or more on it, so it uses a resistor in
the 330Ω–3.3kΩ range.  So those same 100μA will appear, and the LED
will start to light up, at 0.033–0.33 volts above the LED’s forward
voltage drop (typically 3V).  At 100V the LED will have 30–300mA
running through it and will illuminate brightly.  XXX the resistor
will explode

XXX Hmm, I need to rethink this a bit.  Even at 3.3kΩ the resistor
dissipates 3 W at 100V.

The resistors can be pulled from broken or surplus power supplies,
which commonly have large resistors in them, and identified using the
resistor color code, without a need for a multimeter.  It will need to
be verified that they do conduct electricity.

By attaching the some-useful-voltage detector to one side of the
output of a known-good power supply, you also get a diode and
continuity tester.

#### A variable-voltage linear power supply from a power transistor and a potentiometer ####

Once you know a given regulated DC power supply works, you need to be
able to derive other DC voltages from it.  Suppose it’s 12V, the
highest-voltage rail on an ATX power supply (and typically provided
with a lot of current).  You can rig a 10kΩ potentiometer across it to
get a variable voltage reference, then feed that into the emitter (or
gate) of a power transistor whose collector (or drain) is connected to
the appropriate power-supply rail, thus giving you an emitter (or
source) follower.

This allows you to get whatever regulated output voltage you want, up
to a diode drop below the input voltage.  But how do you know what
voltage you’re getting if you don’t have a multimeter?

#### A string of LEDs with parallel resistors to measure power supply output voltage ####

Three or four LEDs in series to ground, ideally a 1.5-volt indicator
type rather than a 3V illumination type, can provide some kind of
indication of how high the input voltage is.  At below 1.5 V, no LEDs
will light.  At 1.5 V, the bottom one will light, fed by a string of
resistors to it from the voltage input.  Successive resistors in
parallel with the other LEDs will develop enough voltage to light
those LEDs as the current rises; this requires them to have lower and
lower resistances.

#### A Wheatstone bridge to measure unknown resistances and compare voltages ####

On one side of the bridge we use a potentiometer (presumed linear)
with a knob glued to it; the other side pits the unknown resistance
against a known resistance.  Rather than Wheatstone’s galvanometer
across the middle, we use a pair of antiparallel LEDs in series with a
small protective resistance.  This may require that the input voltage
be rather high, tens of volts, to get good precision.

With an AC source, I think this setup also works to measure ratios of
capacitances or inductances.

Then, it should be possible to replace the crude LED pair with a
delicate differential pair of NPN transistors.

These detectors of voltage differences can also be used to directly
compare voltages, for example to calibrate positions on the
potentiometer knob on the linear power supply against known regulated
voltages, either from a multi-voltage power supply or from a 7805 or
something.

#### A VCO to measure voltages and resistances more quickly and precisely ####

There are lots of circuits for this but I don’t know which ones are
simple, free of soakage, thermal coefficients, and whatnot.  But if
you build one you can hook it up to a speaker to listen to your
signals; one of the 200-in-1 projects does this.

