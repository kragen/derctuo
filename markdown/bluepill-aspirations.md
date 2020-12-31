So I got a Blue Pill for US$4.  This one has a [CS32] rather than an
STM32 in it; it says CKS32F 104C8T6 NMCM4 2013 A or something on it.
Later today the ST-Link should arrive.  All I know so far is that the
board weighs 6 g, measures 22 mm × 52 mm without the pin headers
soldered, and lights its power LED when plugged in over USB.  So I am
currently in that delicious limbo where everything is possible and
nothing is yet difficult.

[CS32]: https://www.cnx-software.com/2019/02/10/cs32-mcu-stm32-clone-bluepill-board/

(Aha, now my ST-Link has arrived, and the board powers up with it
correctly.  The programming connector is labeled with reasonable
clarity 3V3 SWIO SWCLK GND, matching 8 of the 10 pins on the ST-Link;
missing are 5.5V, RST, and SWIO.)

CS32
----

People have [reported success flashing these][0] with openocd after
tweaking its config a bit; they also mention that the GD32 mirrors
Flash into RAM, presumably at startup, which presumably causes slower
startup but apparently faster run speed and maybe lower power
consumption; the CKS part may or may not be the same.  The CKS chip
supposedly has 64K of RAM instead of 20K (or 40K?), which might be a
plus.  Also, 128K of Flash instead of 64K.  But then others in that
thread reported that it really only has 20K.  And so does the
datasheet.

[0]: https://www.eevblog.com/forum/beginners/unexpected-idcode-flashing-bluepill-clone/

It was hard to find [the Chinese datasheet][1] but I did finally find
it in an [stm32duino forum thread][2].  [Another page of the forum
thread][3] has [another datasheet][4].  It seems to have two 12-bit 1
Msps ADCs, some kind of DAC including an LFSR, 2 I²C channels, 2
18Mbps SPI channels, CAN (!), 3 USARTs, JTAG, USB, an RTC, etc.  (I’m
not sure the STM32 part has all of these!)  Officially the clock only
goes up to 72 MHz.

[1]: https://stm32duinoforum.com/forum/upload/CS32F103%20%20.pdf??? "7960370 bytes, 483 pp."
[2]: https://stm32duinoforum.com/forum/viewtopic_f_3_t_4522.html
[3]: https://stm32duinoforum.com/forum/viewtopic_f_3_t_4522_start_40.html
[4]: ??? "2623596 bytes"

Apparently you can run it at 80 MHz, though not the 120MHz or 128MHz
of the GD32 parts.  User Macbeth says they couldn’t get the stm32duino
bootloaders to work on their *possibly* CKS chips (which claim to be
brand-name STM32) but could get mecrisp to work:

> I figured these dodgy STM32s just have a crippled USB port but then
  I flashed ‘mecrisp’ which is an implementation of Forth running over
  its own USB serial and it works perfectly! Very odd.

Maybe it would be worthwhile to look at the [boot ROM][5] to compare
it to ST’s with `$ st-flash read system.bin 0x1ffff000 2048` using the
st-flash program from Arduino’s STM32Tools.  User ag123 says the
bootloader matches ST’s.

[5]: https://stm32duinoforum.com/forum/trebisky/stm32f103/blob/master/serial_boot/boot.txt

Aha, I measured R10; it’s 1.5kΩ as it ought to be, not 10kΩ as in the
original Blue Pill design.  Hopefully the silkscreen hasn’t been
switched around, so that R10 is the correct R10.

Possible things to try
----------------------

Clearly the first thing to do is to try to get it to blink an LED.
Then it would be nice to get a USB bootloader programmed to see if
that works, so that I can program it (with the Arduino IDE?) without
the ST-Link dongle.

The next thing to try is probably something with audio: generate some
bytebeat and try to feed it into my microphone port (or this speaker’s
audio in) with a hacked cable.  Hmm, better find those audio plugs I
scavenged... although alligator clips or copper wire twisted around a
phono plug would work too.  It has real DACs, too, not just PWM.

Next thing is probably hooking up these potentiometers I have here as
analog inputs.

Next thing after that is to get a voltmeter running with analog
inputs, limiting diodes, and a voltage divider or three.  (Though see
[Multimeter metrology](multimeter-metrology.md).)  If USB
serial is working then I can transmit the result over USB serial.
Otherwise I’ll be limited to blinking an LED or doing speech synthesis
or something.  Or a modem.

Being able to run it off two AAA (or AA or C) cells, or a CR2032,
would be pretty handy.  I should try that.  In theory it should be
good from 1.8V to 3.6V.

From voltmeters, the next steps branch out: oscilloscope-style data
acquisition at 1 Msps, ohmmeter, milliammeter, and thermometer, using
some random component as the temperature sensor.  In particular I want
to calibrate it to use these quartz-halogen lightbulbs as temperature
sensors, but I suspect that at room temperature.  Averaging ADC
readings over 16 seconds should in theory permit adding an extra 12
bits of precision to the ADC, giving us 24 bits of precision.  But
what is the temperature sensor being measured *against*?

As Weston said in his Weston-cell patent, a voltage standard that
varies with temperature (like the Daniell cell or the Clark cell) is
dependent on the thermometer for its precision.

I think it has some kind of ability to write to Flash under program
control.  This should enable me to tell whether it was turned on while
disconnected from USB.

I’d like to then see about using scavenged LEDs as light sensors and
photocells.  Three or four LEDs in series ought to be enough to
provide it with 5V which can then get regulated down, but this
probably involves disconnecting the power LED.  (Too bad it’s in the
wrong direction to use as a photocell...)

It would be good to verify that the part really does have 128K of
working Flash; some forum users report only 64K.

Some kind of component ID thing?  Like an LCR meter.  This is pretty
similar to complex impedance sensing, which would be useful for
identifying materials.  Touch sensing and electrical impedance
tomography seem like promising next directions to move in.

A logging wattmeter should be pretty simple, but will need multiple
boards.  And megohm resistors.

All of the above (except generating audio and blinking an LED) is just
measurement exercises.  Although those are useful, some actuation
would be super helpful too.  The simplest thing is probably turning a
motor (or an ATX power supply?) on and off with a transistor.  Maybe I
can use a triac or something to put a timer on the water pump.

Of course the noblest of all actuators is the spark, capable of
marking metal, perforating plastic, and deodorizing apartments.  The
spark can also sense; in particular it can detect flame.  The board
ought to be able to time such events to within ±6 ns.

Given some kind of temperature sensor and heating element, it ought to
be able to do PID control of temperature.  These 240V 70W
quartz-halogen lightbulbs are under 150Ω at room temperature, over
180Ω at 100°, and (240V)²/70W gives 822Ω at operating temperature;
cf. [Thermistors](thermistors.md).  Although XXX I may have
miscalculated something in there... anyway, if the resistance stays
the same, at 83 mA I should be able to get a watt out of them, 120 mA
for 2W, 190 mA for 5W, 260 mA for 10W, 373 mA for 20W.  At 3.3 V the
most I can get is 76 mW, but 12 V should be able to give 1 W.

Dimming some colored LEDs also seems promising.

If I can rig up some kind of weighing scale, maybe using capacitive
sensing, I ought to be able to measure materials more precisely.

A couple of electronic gadgets I’ve been putting off doing anything
with are this PAL acoustic delay line and these linear servos from
inkjets.

Speaking of PAL, bitbanging PAL or NTSC would be a pretty awesome
thing to do, although in practice probably bitbanging VGA would be
easier and more useful.

Another extremely awesome thing to do with it would be to get it to be
a USB HID so that it can type on my computer.