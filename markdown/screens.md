<!-- -*- org -*- -->

It’s 02020-11-15.  Four days ago my options for microcontroller output
were limited to one four-digit LED display I’d painstakingly
desoldered from a microwave, some other LEDs I’d painstakingly
desoldered from other random discarded electronic equipment, and the
microwave’s beeper.  Now the covid quarantine is over and I’ve been
able to visit my apartment (I’ve spent the last 7½ months at my
girlfriend’s apartment) and now I have a number of additional options. 

    | display    | w(mm) | h(mm) | type | shows   | color | pins | conn        |
    |------------+-------+-------+------+---------+-------+------+-------------|
    | DeV96      |    50 |    18 | LED  | 4 0-9   | red?  | 14   | SIP 2.54mm  |
    | microwave  |    45 |    12 | LED  | 4 0-9   | green | 14   | SIP 2.54mm  |
    | breadboard |    50 |    18 | LED  | 4 0-9   | red   | 36   | DIP 2.54mm  |
    | Kenko      |    52 |    12 | LCD  | 8 0-9   | grey  | ≈32  | flex 1.26mm |
    | Kadio      |    60 |    18 | LCD  | 12 a-z? | grey  | ≈75  | flex 0.8mm  |
    |            |       |       |      | +12 0-9 |       |      |             |
    | Franklin   |    53 |    18 | LCD  | 25 a-z? | grey  | ≈105 | flex 0.5mm  |

Some of these are sort of package deals with a keyboard and maybe a
battery and case, which tempts me to rebrain them (see
[Rebraining](rebraining.md)), while others are just displays.

The LED displays are suitable for PWM fading; not sure if this will
work with the LCDs.  Nobody ever does PWM fading of 7-segment LED
displays, which is going to rock.  Also I think LED displays would
look much better shining through cloth, another thing nobody ever
does.  These LCDs are all reflective LCDs, so they would have the best
readability in daylight.

Cellphone screens are pretty interesting, especially SPI Nokia
screens, although I don’t happen to have any at the moment.

DeV96
-----

A guy at a hackerspace gifted me this display he’d made on perfboard,
evidently on 01996-10-13.  It has an 8-key keypad, apparently
configured as a 2×4 matrix with diodes, and five BC548B transistors
onboard; four of them are hooked up to turn on a particular digit of
the 7-segment LED display.  I traced the wiring out before, but I
forget if you can fully control the display from offboard with the
14-pin header he’s provided, or if it’s partly under control of the
keyboard.

The 7-segment displays are helpfully in a socket, so not only can you
replace them if you burn them out, but you can easily transplant them
into other things.  Each has a decimal point.

This may be the easiest option to use, since it already has
current-limiting resistors (probably sized for 5V) and
common-electrode transistors with current-limiting resistors on it.
The individual segments are driven from offboard, so you need to
source or sink (source I think) enough current to light them up.  It’s
also one of the largest displays, which will make it easy to read.
It’s probably not using high-brightness LEDs, which means it won’t be
as bright as some other options, especially if run off 2V or 3V.

I suspect 12 pins of the 14 pins, plus power and ground, would be
enough to drive the display, and it would probably work down to 2V.

Microwave
---------

The front-panel display from the microwave oven I ripped apart has
four 7-segment digits without decimal points, a colon with separately
illuminable dots, and ten miscellaneous microwave-oven-related
ideographic indicators above and below.  The digits are slightly
smaller than the DeV96 digits, but the component as a whole is much
smaller, and it lacks any onboard resistors or transistors.  There are
nine cathodes (?) for the individual segments of a digit, then five
common anodes (?) to select the digit; one of the five “digits” is the
colon and two of the miscellaneous indicators, while each of the other
digits controls two more miscellaneous indicators.  So you can control
all the digits proper with 11 pins.

The digit segments light up faintly green on the milliamp or two from
my multimeter’s diode-test mode, but are presumably intended for a
much higher current.  I could probably stress one of the colon dots
until it blows to get an idea of what they could stand, but 20mA is
probably safe, and close to the limit of what my microcontrollers can
provide anyway.

It’s labeled GAL9801-OI on one side, but GAL-something was the
microwave’s model number, and no datasheets seem to be forthcoming.

I was able to get the microwave’s somewhat yellowed front panel off in
one piece; it has a flat-flex cable (dated “11/18/99”) which went into
a 12-pin cable-pinch connector on the mainboard (where the display was
soldered), and I was able to desolder that connector, which has the
same breadboard-friendly 2.54mm pin spacing as the display.  Its
polarized front window of course can fit the display in it.  It has 17
membrane-keyboard buttons labeled with microwave-related things and
connected in some kind of matrix with resistive ink on flat-flex to
the 12-pin connector.  I was able to detect a press on one of them
with the multimeter; it read as about 120 Ω.

I’m not sure, but I think these are “bright green” 3-volt LEDs rather
than the older 1.7-volt-or-so older green type.  So running the
display at 20 mA per segment would probably cost 420 mW worst-case.
But at an equivalent brightness it probably costs less power than the
DeV96 display.

I could very reasonably tack the display in place inside the front
panel with some silicone or something, then mount a control board and
even power supply inside, repurposing the shitty membrane microwave
keyboard for some nobler purpose, perhaps covering it with labels.
The 7 segment-selector pins could presumably be shared with at least 6
and probably 7 of the pins for the keyboard, so you’d probably only
need 16 pins between the keyboard and the display.  The 18-GPIO
ATTiny2313 has enough pins for that, but not to do much else, and no
ADC, which would be useful for many of the things I want to show on
the display.  But it can blow or suck 40 mA per I/O pin, so it could
drive the display at a dimly readable intensity without any external
hardware at all, and probably very brightly if supplied with four
per-digit high-side transistors.

If I’m wrong and they’re actually low-side transistors, it might be
able to drive the transistors with its internal pullup resistors!
50kΩ worst-case at 5 volts gives us 100 μA, and with an unremarkable β
of 300, that would give us... 30 mA.  Which is worse than what it can
do itself.  *sad trombone*  It might happen to be higher than that, or
you might be able to use a darlington.  But then you can also get
transistors with integrated base resistors.

An STM32 or CKS32 is more appealing in many ways because it has a
kickass ADC and a lot more I/O pins, but the STM32 can only source or
sink 25 mA per pin, and can only run at 3.3 V.  (I think the CKS32 is
the same, but I have a harder time reading the datasheet.)  And I only
have two, so if I blow one up, I will be a lot sadder than if I lose a
2313 or a few ATTiny45s.

As for 45s, I have dozens, and it might be possible to gang several of
them up to drive the display, maybe using two wires for I²C (“TWI”
according to Atmel) and leaving four GPIOs per chip, or three GPIOs
plus an ADC.  Four of them would be enough to drive the display and
read the keyboard, though you’d probably still want external drive
transistors for the display digits.

Breadboard
----------

At my house I picked up a breadboard that has two 18-pin two-digit
7-segment red displays in it, probably from 2006.  They’re labeled
LDD5111-11.  These have decimal points and need external current
limiting.  They’re probably the largest digits I have available.  The
datasheet, from 1996, says they have a surprisingly rational pinout
with 16 individual per-segment anodes and two common cathodes.  You
could gang up the corresponding per-segment pins to get a 12-pin
interface, but you’d probably want to drive them at more than 40 mA
per digit, so you probably still want external low-side drive
transistors for the common cathodes.

The datasheet (“Jameco Part Number 24723”, “Ligitek”, for a whole
family of such displays) says these are red GaP LEDs at 697 nm with a
90-nm bandwidth and a 1.7–2.8 V forward voltage drop, with 2.1 V being
typical; 0.5 millicandela minimum at 10 mA, 0.8 millicandela typical;
either 40 mA or 15 mA maximum current “per chip” (or 200 mA or 60 mA
pulsed at a 10% duty cycle), and either 110 mW or 45 mW maximum power
per chip; and 10 μA reverse current “absolute maximum”.  I don’t know
which power rating to use; the higher rating is “SR”, and the lower is
“H”, but I suspect it’s the lower one, because that’s the one that is
dimmer at 10 mA.

I think “per chip” means “per LED” rather than “per digit” or “per
package” because 15 mA per digit would be 2 mA per LED, just too low.

This may be the easiest option of all, as well as the most readable,
indoors anyway.

So, to run these manually off 5 volts, I probably want 8 per-segment
resistors of 220–2200Ω, and maybe 55Ω when driving them with a
microcontroller.

A little testing shows that the forward voltage at 14.5 mA is about
2.1 V, and they aren’t terribly bright at that current.  I may try
burning out a decimal point in one to see how bright they go.

Kenko KK-9835TS
---------------

I have a talking Kenko KK-9835TS desk clock/calculator from 2007,
which has an extremely abrasion-resistant transparent keyboard, an 8-digit
7-segment LCD display, a dynamic speaker with a plastic cone, and lots
and lots of wasted space inside.  It runs off two AA batteries.
It’s about 155×120×32 mm and weighs 143 g.  The mainboard is
of course a chip-on-board with a blob of epoxy over it.

This display probably has the largest LCD digits of the displays I
have and would thus be optimal for daylight readability, and the flex
cable to the LCD has a very comfortable 1.26-mm “pin spacing” between
its 32 or so lines.  The slightly mushy 26-key keyboard is also
connected via a flat-flex cable, making it ideal for rebraining.  The
keys are easily replaced (I’ve turned the digits upside down) but it
might be even more interesting to reformat them by taking them out,
grinding the opaque backing off the back, and replacing it with a
custom-printed sticker.

Driving 32 LCD lines in software probably requires 32 GPIOs and a
certain amount of attention to properly alternating the polarity so
you don’t electrochemically degrade the display.  But it doesn’t
require much voltage or current.

Kadio KD-82TL
-------------

I have a Kadio KD-82TL copy of a Casio scientific calculator from
2002, which I’ve written about previously in Dercuano.  It has a
50-key very mushy keyboard and a two-line display, one line of which
is something like 12 5×7-pixel bitmaps (used for displaying formulas
and programs) and the other line of which is something like 12
7-segment digits with decimal points.  (It also has 11 indicators for
calculator modes.)  It’s about 85×165×30 mm and weighs 139 g with the
cover on.

This is also very appealing for rebraining; again, both the keyboard
and the screen are connected to the brainboard with flat-flex cables,
and it runs off a couple of AA batteries, which I have removed because
they had run down.  It comes with a protective shield that slides over
the face to protect it from damage.  There is less wasted space inside
than on the Kenko.

Driving the LCD seems potentially a lot more challenging than on the
Kenko: there are twice as many pins, and they are less than a
millimeter apart.  Worse, I seem to have torn the flat-flex cable off
the LCD edge where it was connected, and so I need to figure out how
to re-establish contact.

Franklin TES-118A
-----------------

This alphabetic LCD display is in a “Franklinⓡ model TES-118A
Spanish-English translator, ISBN 1-56712-689-8” Beatrice and I bought
in 2005.  The whole device runs off a CR-2032 coin cell, measures
about 105×70×15 mm, weighs 64 g, and has a pushbutton-latching domed
clamshell cover that covers everything but the power button and the
screen, which is recessed so far into a hole in the clamshell that it
still hasn’t broken, though it’s a little scratched.  Under the
clamshell cover is a very mushy rubber QWERTY keyboard with arrow
keys, five ideographic keys for different “applications”, and six
miscellaneous buttons: menu, power, clear, back, space, and “conj”.
Since I don’t have a CR-2032 handy, I’m not sure how many pixels are
on the display, but it’s intended to display a few words at a time.

Opening the case was a little tricky; the clamshell unclipped from two
steel rods acting as hinge pivots, three Philips screws under the
battery cover came out, and then the two halves of the bottom of the
case unclipped with fingernail pressure — at which point the tiny
hinge pivots and plastic clamshell latch button fall out.  I was glad
to have a styrofoam sandwich tray underneath it to catch the parts,
but still had to shake some out of my clothes.

This is unbelievably appealing for rebraining: a pocket computer
ruggedized to survive being sat on, with a daylight-readable tiny LCD
that can run on microwatts, with a space for a coin cell!  But it
would be very challenging: the LCD connector not only has many pins
(about 100) but also the keyboard is on the same PCB as the
chip-on-board epoxy brainblob.  And there’s not a lot of wasted space;
from the dimensions above you can see that it’s about 0.6 g/cc.  So
the most feasible approach might be to grind away the chip-on-board
and wire up the new brain to the now-floating connections, rather than
try to use the flat-flex connector.

There’s actually a bit of extra space under a silver-covered “keyboard
template” which snaps over the keyboard.  Like a millimeter or three.
Hard to use but it’s about 15% of the device’s total volume.
