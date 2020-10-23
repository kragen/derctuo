Dave Jones, author of EEVBlog, has been playing with these Padauk
one-time-programmable PMS150 and PMS150C microcontrollers which cost
2.7¢ (1 yuan I guess?), and his fans on the EEVBlog Forums have put
together the [Free PDK][2] and easy-pdk-programmer-hardware projects
to make the Padauk chips more usable (so you can have, you know, `for`
loops and function arguments).

[2]: https://github.com/free-pdk/

So consider a personal computer consisting of a 5¢ USB micro-B jack,
three 8¢ CPUs, three 5¢ bypass capacitors, a [60¢ 8-kibibyte 20MHz SPI
SRAM module][0], a 15¢ 2-megabyte NOR Flash chip, a 30¢ piezoelectric
buzzer, and a resistive touch input surface made of paper and powdered
graphite (rather than 3M Velostat) using [Chris Harrison's "Electrick"
electrical field tomography technique][5].  The total BOM cost is
US$1.49, plausibly reducible to under US$1, and it is probably capable
of executing about 24 million 8-bit Padauk instructions per second, or
2.4 million interpreted instructions per second, superior to an
original IBM PC.  It might also be feasible to bitbang SPI to a
MicroSD card, to bitbang PS/2 keyboard input, and to bitbang video
output signals for NTSC, PAL, or VGA.

[0]: https://www.digikey.com/en/products/detail/microchip-technology/23K640T-I-SN/9643732
[5]: https://chrisharrison.net/projects/electrick/electrick.pdf

Overview of the Padauk microcontrollers
---------------------------------------

In [EEVBlog #1306][1] Jones starts by buying a STM32F072C8T6 on
Digi-Key for, I guess, the Easy PDK Programmer Hardware, then
demonstrates how to instead upload the BOM CSV file from
easy-pdk-programmer-hardware to LCSC's website.  Then he uploads the
Gerbers to JLCPCB and PCBWay for fabrication as a demo.  I haven't
watched the rest of the series yet, because there's only so much Dave
Jones I can deal with in a day.

[1]: https://www.eevblog.com/2020/05/19/eevblog-1306-1-of-5-3-cent-micro-open-source-programmer/

Digi-Key doesn't carry the PMS150 or other Padauk micros; you have to
buy them via LCSC or some other Chinese distributor.  EEVBlog forum
user [spth reports that they're available for less than 1¢ on
Taobao][3].

[3]: https://www.eevblog.com/forum/blog/eevblog-1306-(1-of-5)-3-cent-padauk-micro-open-source-programmer

[Jay Carlson wrote a review of the PMS150 family of
microcontrollers][4].  He explains that the family runs from 512–4096
words of program memory and 64–256 bytes of RAM, all running at up to
16 MHz (normally 8 MIPS).  The PMS150C is 3.18¢ and has 64 bytes of
RAM, 1024 words of ROM, 6 I/O lines (including an 8-bit PWM line), and
runs on 2–5 (?) volts at 450 μA/MHz.  The cheapest one with 256 bytes
of RAM is the 8.64¢ PMS133, which also includes a 14-bit ADC, 18 I/O
lines, 3072 words of ROM, 2 8-bit PWM lines, 3 11-bit PWM lines,
running on 2.2–5 volts at 750 μA/MHz.

[4]: https://jaycarlson.net/2019/09/06/whats-up-with-these-3-cent-microcontrollers/

He says they can run on a high-speed 16MHz internal oscillator
("IHRC"), as well as a low-speed internal oscillator of tens of kHz
("ILRC") for lower-power operation.

Carlson is mightily impressed that some of these processors have
hardware multithreading, context switching on every instruction,
though with only two threads.  He tried this out with a WS2812B
adaptor, for which he had to bump the clock speed up to 18 MHz.

### Power efficiency: 1 μW sleep, 1800 pJ/insn ###

He also measured that the PMS150C only used 350 nA on 3.3 V in sleep
mode, concluding, "A CR2032 battery could power this thing in sleep
mode for 10-15 years — the limiting factor would be the self-discharge
of the battery itself."

By comparison, my notes in Dercuano say the datasheet says a STM32L in
"stop" mode uses 540 nA with the RTC running and 290 nA with the RTC
stopped, which I calculated at 134 years of a CR2032, which of course
won't physically last that long, as Carlson said.  This is comparable,
but its low-power modes sound like more of a pain to wake up from.  I
estimated that the STM32L0 requires 210–400 pJ per 32-bit instruction;
the PMS150C's 450 μA/MHz at two clocks per 8-bit instruction at 2.0
volts would be 1800 pJ per 8-bit instruction, about an order of
magnitude less efficient.  However, the STM32L0 costs US$1.50 rather
than US$0.03.

Memory
------

If you want the Padauk chip to be a general-purpose computer rather
than translating some voltage levels or performing a biquad filter on
PCM data as Carlson did for his review, you're going to need some
external memory, at least 4 KiB or so.  Adesto (the new brand for
Atmel's SPI Flash chips and so on) has published a bizarrely titled
whitepaper about "AI memory", in which they claim their new EcoXiP
line of nonvolatile memory offers a better tradeoff for SPI
execute-in-place uses, because large SRAM and especially PSRAM chips
use a lot of power, while large SPI nonvolatile memory chips have
historically been intended for booting and are consequently kind of
slow.

The upshot seems to be that, if the firmware on the Padauk chips
themselves is running some kind of interpreter on instructions it
loads over SPI, you might be able to run them faster if you use either
EcoXiP or SRAM (or PSRAM), than if you try to get by with *only*
nonvolatile storage.  This is the reason for including the 60¢
Microchip 23K640T SRAM chip in the design sketch at the top of this
document.  [Microchip's datasheet][9] claims it requires 4 μA of
standby current, which is 10× more than the microcontroller, and 3 mA
of read current at 1 MHz, which presumably scales to 60 mA at 20 MHz,
as much as all three microcontrollers put together.  (Also that
particular chip won't run at 5 V, but presumably there are comparable
or better chips that will.)

[9]: http://www.microchip.com/mymicrochip/filehandler.aspx?ddocname=en538990

Prospects for self-hosting a development environment
----------------------------------------------------

EEVBlog's fans ported SDCC to the hardware, under the architecture
names "pdk14" and "pdk15", so new these devices have a free-software
toolchain.  SDCC is a pretty decent C compiler, mostly ANSI C99, used
among other things to write CP/Mish.  Could it be made to run on the
Padauk platform?

SDCC is in all about 10 megs of source code, compressed, and only
"officially" supports the 386 and amd64 platforms these days, on
MacOS, Linux, and Microsoft Windows, including Cygwin.  It's built
using autoconf and maybe automake, though, and the ChangeLog is full
of mentions of platforms like FreeBSD, OpenBSD, NetBSD, sparc64,
SPARC, ARM, PowerPC, ppc64, and even the Alpha (though that was in
2006).  Support for compiling it with Borland C was removed in 2009.

The stripped executable is about 2.8 megs on i386, roughly the
internal RAM of 10949 PMS133s, which would cost US$946 (BOM cost).  So
clearly it wouldn't be a straightforward port; you'd need to use some
kind of external memory, which probably means using some kind of a
virtual machine.

On this Atom netbook SDCC 3.5.0 takes 40–52 ms of user time to compile
hello.c for Z80 (this old SDCC doesn't support Padauk) as follows:

    $ cat hello.c
    #include <stdio.h>

    int main() { printf("hello, world\n"); return 0; }
    $ time sdcc -mz80 -c hello.c

    real    0m0.066s
    user    0m0.040s
    sys     0m0.016s

Valgrind claims that this takes 36'131'256 instructions, so you
probably need at least to run a few million 32-bit-equivalent
instructions per second to make SDCC usably fast.

Fred Brooks made a famous declaration (in _The Mythical Man-Month_?)
that the System 360 linker around 1968 was probably the most advanced
overlaying linker that would ever be written, since virtual memory
made overlays obsolete.  But SDCC's targets mostly don't have virtual
memory, though many do support banking, so SDCC supports overlays
today.

The most immediate problem with compiling SDCC with itself is that it
does `#include <memory.h>` but does not provide such a header file for
its targets.  This is not in itself a terrible problem (SDCC does
provide `<string.h>`) but it does suggest that nothing like this has
ever been tried.

Another problem is that, even if you can get the Padauk chips to
emulate one of its supported platforms, it doesn't support generating
code for any of the targets mentioned above that the ChangeLog
suggests it's been ported to, or indeed for any target that GCC
supports.  (It used to have AVR support, but that has been removed.)

So to get a self-hosting compiler on the Padauk chips, SDCC doesn't
seem like a particularly promising starting point.

Building an in-circuit emulator for the chips (important because
they're mostly one-time programmable and don't have a lot of extra
pins to devote to debugging) seems like it would be pretty difficult
to do with the chips themselves.  If you were willing to accept an
order-of-magnitude slowdown it might be reasonable.

Building a programmer board using more of these microcontrollers
(rather than, say, an STM32) might be feasible.

Portable power
--------------

What if you didn't have to plug the fucking thing in all the time?

In [my note on aluminum-air batteries][7] I noted that ghetto
aluminum-air batteries reportedly have an energy density of 7 or
8 MJ/kg, which is to say, 7 or 8 joules per milligram (!).  At 1800 pJ
per instruction, each milligram of aluminum buys you 3–4 billion
instructions, a few hours of computing at Commodore-64 speeds, or
maybe an hour at IBM PC speeds.  (On the other hand, if you're relying
on a piezo tweeter for output, you may burn through your energy
noticeably faster.)  Using my estimate from the Dercuano note on
keyboard-powered computers, that basic word processing functionality
needs about 30 μJ per keystroke (mostly to update an e-paper screen
not considered in this design), each milligram of aluminum buys you
around 33000 keystrokes, about a chapter's worth of writing.

[7]: aluminum-air-batteries.md

If we're running all three microcontrollers at their full 16-MHz
speed, we're using about 22 mA or, say, 60 mW.  This is 0.6 cm² of
full sunlight, or 6 cm² if you're using those 10%-efficient amorphous
solar cells from solar calculators, or 48 m² if you're using [cuprous
oxide solar cells made in your kitchen from expensive household
materials][8].

[8]: https://scitoys.com/solar_cell.html

If you had a capacitor or battery you were efficiently charging and
discharging, then one second of charging at those 60 mW (60 mJ) would
pay for 30 million instructions or 2000 keystrokes of word processing.

One gram of aluminum would be enough to run the computer for 3–4
trillion instructions: almost 40 hours at full speed, or almost 4000
hours, 5 months, at Commodore-64 speeds.

(None of these figures include power consumption for electric field
tomography, VGA signal generation, etc., or more problematically,
the RAM mentioned above.)