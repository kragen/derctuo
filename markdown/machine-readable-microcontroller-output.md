Suppose I have a super minimal microcontroller that generates some
data and I later want that data somewhere else.  Like an ATTiny2313,
say (US$0.67 in quantity 1 from Digi-Key, US$55 for 100, but not a
smart buy in 2020).  What are my options?

UART
----

UARTs can be used with serial ports where those are present.  But
typically they require a quartz crystal to meet the timing
requirements of RS-232; even the ceramic resonators used on low-end
Arduinos are insufficient.  Lots of times I want to run off the ±10%
RC resonators in these computers, which can be maybe trimmed to ±1%,
or maybe not.

If you can get a well-controlled computer to send you some data first
at what it thinks is an accurate baud rate, you may be able to examine
its timing to recalibrate your clock.

But many modern computers omit serial ports.

SD cards
--------

SD cards support an SPI interface through which you might be able to
write files onto the filesystem.  To deal with RAM as small as the
ATTiny2313’s (128 bytes!) you probably need to read the same sector
from the SD card more than once if you’re not sure where you’re
looking in it.  This requires at least four pins (SCK, MISO, MOSI,
/SS) and might be possible to pull off.

Then you can disconnect the SD card from the microcontroller and plug
it into wherever you want to read the data.

Pretending to be an SD card
---------------------------

I’m not sure if this is practical, since SD cards normally support
other interfaces that are hairier than just SPI, but maybe so.  Maybe
I should investigate further.  Lots of computers have SD card
interfaces on them now and they’re easy to buy.

PS/2 keyboard
-------------

The [AT/PS/2 keyboard/mouse
interface](http://www.burtonsys.com/ps2_chapweske.htm) is a two-wire
open-collector bus with separate data and clock lines.  (Plus +5V at
up to 100mA and ground.)  Chapweske reports success using PIC internal
pullups and setting pins to output 0 to pull them low.  Fortunately
the *peripheral* always generates the clock signal, so in theory you
ought to be able to use a weird clock rate, and in theory the clock
speed should be 10–16.7 **k**Hz.  That is, 12.9 MHz ±23% — even the
uncalibrated RC oscillator can beat *that*!  And you have to check
every 10 ms or less to see if you need to generate a clock to receive
a host-to-peripheral communication.  Other than that it’s a matter of
sending 11-bit packets, each a byte with some start and stop bits.

So you could imagine acting as a keyboard and typing the data to a
computer when plugged in.  In theory you’re not supposed to hotplug AT
and PS/2 keyboards but I’ve never burned up a motherboard yet doing
it.

There’s even a [PS2Keyboard
library](https://playground.arduino.cc/Main/PS2Keyboard/) in Arduino
[from PJRC](https://www.pjrc.com/teensy/td_libs_PS2Keyboard.html) that
already implements the protocol, but it’s the other way around — it’s
so you can plug a keyboard into your Arduino!

Unfortunately many modern computers have replaced the PS/2 interface
with USB, which is much harder to bitbang.

Low-speed USB bitbanging
------------------------

The well-known [GPL V-USB
library](https://www.obdev.at/products/vusb/index.html) supports
bitbanging low-speed (1.5 Mbps) USB on “any AVR microcontroller with
at least 2 kB of Flash memory, 128 bytes RAM and a clock rate of at
least 12 MHz” and claims to even support being clocked from a 12.8-MHz
or 16.5-MHz internal RC oscillator — but not the ATTiny2313’s
7.3–9.1-MHz (??) internal oscillator. Also, it would occupy most of an
ATTiny2313: “Only about 1150 to 1400 bytes code size.”

And there’s a [similar project called 16FUSB for
PICs](http://dangerousprototypes.com/blog/2012/05/08/16usb-open-source-low-speed-bit-bang-usb-interface-for-pic16fs/).

[Atmel’s app note AVR291 on the
ATMega32U4RC](http://ww1.microchip.com/downloads/en/AppNotes/doc8384.pdf),
which has USB hardware, explains that low-speed USB requires 1.5MHz
±1.5% (or, on p. 9, ±1%, but [Silicon Labs says it’s
1.5%](https://www.silabs.com/community/interface/knowledge-base.entry.html/2004/03/15/usb_clock_tolerance-gVai)),
and that this precision is within the capability of the AVR family’s
oscillator calibration.

Bitbanging a USB interface offers the perhaps more appealing
possibility of offering a USB mass storage interface.

Centronics parallel port, Raspberry Pi GPIO, and other GPIO
-----------------------------------------------------------

A Centronics parallel port like the fuchsia one on the back of this
old tower here used to be common, but isn’t now.  But if you have one,
it’s easy enough to bitbang SPI over it, or over the GPIO pins on a
Raspberry Pi, or an Arduino, or whatever.  I mean that’s how the
ArduinoISP sketch works.

Speaker modem
-------------

If the amount of data to be transmitted is not too great (and in the
case of the ATTiny2313 you can’t store more than a couple of K on the
chip; larger micros might have 32K or 168K or something; but more
typical is maybe 128 bytes) then maybe you could use a really simple
speaker modem, if the microcontroller can either transmit radio or has
a speaker built in.  It likely doesn’t have to be able to travel over
POTS phone service, so use of frequencies about 3kHz is fine; maybe
frequency-shift keying between 3520 Hz and 5280 Hz would work, for
example.  Three cycles at 5280 Hz is 568.2 μs, and so is two cycles at
3520 Hz.  If this were half the bit interval (“MSK”), this would give
3520 baud.  Some parity bits would probably be worthwhile.  You could
use a square wave and it would still work fine if the volume was high
enough.

It would sound terrible, though.  If you instead used 17000 Hz and
19125 Hz, which are still within the range of most microphones, you’d
have 9 half-waves of one against 8 half-waves of the other, each
taking 235 μs, giving 4250 baud.  And it would be inaudible except to
very young people.

If you packetized the data into packets with 1 header byte, 7 data
bytes, and 2 parity bytes, then you’d get almost 372 bytes per second.

Although wireless, this approach would often work even more neatly if
you could plug the microcontroller device into a microphone jack, like
the Danger camera or the Square card reader.  Typical microphone jacks
provide a 5-volt “bias” or “PiP” or “IEC 61938” voltage on the ring
electrode for electret microphone preamps.  [Reputedly this is
typically in the 340 μA to 2.5 mA range because of a 2kΩ–10kΩ
impedance](https://www.epanorama.net/circuits/microphone_powering.html).
The [Google Android device specification for the 3.5-mm headphone
jack](https://source.android.com/devices/accessories/headset/jack-headset-spec)
says the “mic bias voltage” should be 1.8–2.9 volts and that the “mic
bias resistance” is “flexible” but later says something I don’t
understand about “2.9V mic bias applied through 2.2 kOhm resistor”.
That would be 660 μA and thus a maximum output power of just under a
milliwatt.

Wirelessness and many-to-many nature may be advantages in some
circumstances.  You could imagine several sensors that all log data
over the air in a room at random times or on demand, and one or more
data loggers that demodulate that data and log it.

LED communication
-----------------

The actuator most easily accessible to a microcontroller, apart from
simple wires, is an LED.  LEDs themselves typically support
astoundingly high data rates, up into the megabits per second.  But
most computers that might be able to observe the LEDs cannot observe
them very fast, perhaps with a camera running at 30 fps, 60 fps, or a
slightly higher rate.

You might be able to get 2 or 3 bits of data per frame of video by
modulating the apparent brightness of a single LED, but that’s only
about 180 baud in the best common case.  At 180 baud a 128-byte
message would take almost six seconds: inconvenient but workable for
some applications.

If you’re just after the “wireless” part rather than the “connecting
to big computers” part, you could dragoon one microcontroller into
feeding photodiode or phototransistor data into the big computer so it
can demodulate it, after being transmitted by one or many small
computers.

It’s a shame IrDA worked so badly and was abandoned!
