Reading [Sowing the
Wasteland](https://technicshistory.com/2020/04/19/the-era-of-fragmentation-part-2-sowing-the-wasteland/)
I thought the TICCET idea of using color TVs and, in the absence of a
keyboard, touch-tone telephones as time-shared minicomputer terminals
was pretty interesting.  But driving a TV isn't trivial;
black-and-white is 3 MHz of bandwidth, and a DG Nova isn't really up
to synthesizing that in software like an AVR ATMega328 is, much less
color.  (And this was before VHS and Betamax; even Ampex videotape
machines were huge, expensive things that couldn't freeze-frame.)
Similarly, recognizing DTMF tones isn't that trivial to do in software
either.

And it seems like the system didn't really work out that well:

> But in the event, the Reston system failed to live up to
  expectations. For all the rhetoric of bringing on-demand education
  and social services to the masses, the Reston TICCIT system offered
  nothing more than the ability to call up pre-set screens of
  information on the television (e.g., a bus schedule, or local sports
  scores) by dialing into the MITRE Data General computers. It was a
  glorified time-and-temperature line. By 1973, the Reston system went
  out of operation, and the Washington D.C. cable system was never to
  be.  One major obstacle to expansion was the cost of the local
  memory needed to continually refresh the screen image with the data
  dispatched from the central computer.

But what could it have been?  Is there a plausible way that a 1972
computer system could have provided computation on demand to 128
TV-screen terminals?

Sharing character generators among TVs
--------------------------------------

First, let's reduce the problem a little bit by allowing party-line
collaboration.  You'd have 128 terminals, but only 32 separate screen
images, so when the system was fully used, most people would be
sharing a screen with a group of others.

Now let's suppose the screens are each displaying 12 lines of
40-column ASCII text; 40 columns is about the limit of what you can
fit into NTSC, and 12 lines (like the VT-50) is about the minimum
window that's useful for reading and writing text, although some
machines like the original BlackBerry, the TRS-80 Model 100, and
Motorola two-way pagers have gotten away with less.  If those 12 lines
of text are 8 scan lines each, each screen needs 96 scan lines of
text.  (The other scan lines could be colored with color bars or
something.)

Now, NTSC has 483 visible scan lines (out of 525 total), so you have
almost precisely one fifth of the vertical span of the screen with
text drawn on it.  This means that you can reasonably timeshare a
single font ROM between five screens, so you only need seven font ROMs
to draw 32 screen images.  When the font ROM is being read by the
character generator for one screen, the other four screens in its
group are painting color bars.  (They can have staggered VBIs if it's
desirable to display the text in the same vertical position on every
screen.)

This reduces our screen-painting memory requirements to:

- 7 single-ported font ROMs of 96 5x8-pixel character glyphs, for a
  total of 26880 bits of font ROM;
- 32 40x12 buffers for the 7-bit characters on each screen, for a
  total of 107520 bits of RAM;
- some registers for which TV was viewing which of the 32 "channels"
  and where the cursors are and so forth.  A hardware base-address
  register for the screen buffer might be useful for quick scrolling
  and quick page-flipping, at least if the page you want to flip to is
  already in video RAM.

The ROMs and RAMs need to be read very quickly while painting the
screen.  An NTSC frame is 33.37 milliseconds, so each scan line is 64
microseconds, so each of the 200 pixels across the screen is 318 ns.
However, we can transfer five pixels at a time from the ROM, so we
have 1.59 microseconds to do it, and we can pipeline that with the
following read from the RAM.

40x12 is close to the 40x24 the failed 1979 Prestel system delivered
in England, nearly a decade later, but with color, using a set-top
box.

This works out to 210 bits of ROM and 840 bits of RAM for each of the
128 concurrent users, or 840 bits of ROM and 3360 bits of RAM for each
of the 32 channels.  You also need shift registers for the bits of the
codepoints on the current text line for each five-screen group, and
logic for demuxing the pixels from the character generator to the
right NTSC channel, and things like that, but basically the summary is
that this is a design that would have been dramatically more
economical than VT-52s and things like that.

Generating the rest of an NTSC signal --- the front porches, back
porches, and timing --- is of similar complexity to a black-and-white
TV set.  It's something you can do with a couple dozen transistors,
maybe less.

You do need a separate 3-MHz-bandwidth channel for each of the 32
channels, but cable companies were already in the business of
multiplexing 32 or 64 or 96 channels onto shitty coax, then filtering
and demodulating them at individual TVs.  In fact, TVs at the time
didn't have the option to take "composite" baseband video input; in
the 1980s, my TI 99-4/A came with a cheap RF modulator to modulate its
baseband video output onto either VHF channel 3 or 4, and we had to
use it.  Modulating each of these channels onto a separate frequency
wouldn't have added much to the cost.

The 1969 Nova cost US$3995, but US$7995 once you added 8 kilowords of
RAM (131072 bits).  It had a 1200-nanosecond cycle time, though ROM
took only 300 ns; the 1970 SuperNova had not only a 16-bit parallel
ALU (four 74181s) but also a 800-ns cycle time, and once it had
semiconductor RAM (later in 1970) the RAM cycle time was also 300 ns.
This is plenty fast enough to meet the deadlines described above.

This gives us a reasonable guess as to what the required 107 kilobits
of character code memory would have cost: about US$3500, about US$110
per channel or US$27 per user.  This would have been about two orders
of magnitude cheaper than a 1975 not-yet-available VT52, which sold
for US$1350 even in 1980 (according to terminals-wiki, anyway).

But would it have been responsive and usable?  Touch-tone has a lot of
latency, and the Nova wasn't a super powerful machine anyway.  If we
figure on five memory cycles for an average instruction (typical of
microprocessors a few years later) 800 ns per cycle gives us 4
microseconds per instruction, 250,000 instructions per second, a
little slower than an 8080.  (Wikipedia says the Nova 1200, the
original Nova, executed loads and stores in 2.55 us, accumulator
instructions like ADD in 1.55 us, DIV in 3.75 us (if present), so this
is probably not too far wrong.)  If we figure that handling a keyboard
interrupt might take 100 instructions, it should still be able to do
2500 interrupts a second, although that seems a bit high for a machine
of that vintage.  So it might be rough to do, say, interactive word
processing on it, but simple calculations and programming ought to be
within grasp.

With 32 channels, each channel gets only about 8,000 instructions per
second on average, which is not nearly enough; even operations like
scrolling the screen would take a noticeable part of a second if the
machine were fully loaded.  But if most users are idle most of the
time, it might be feasible.

8 kilowords of memory divided among 32 channels only gives you 256
words of memory per channel, or maybe 128 words once system software
takes up a bunch of space.  This is not very much; for example, it's
less than the text on the screen.  In practice you probably need a
full 32 kilowords of memory, a kiloword per user, if you're going to
have them pair-programming BASIC or making (not-yet-invented)
spreadsheets or something.  And that's probably a US$20k machine, plus
the US$3500 terminal driver system: US$23500 to drive 32 channels to
serve 128 users, US$700 per channel or US$180 per user.  With a little
thought, the machine could easily have included a bulletin-board
system and electronic mail, though entering text on a touch-tone phone
is no picnic, especially since this was 27 years before Tegic T9.

I think this would have been a total steal, though I guess it's
possible inflation is tricking me.

Suppose you wanted to make it actually cool?  Square-wave music like
the IBM PC wouldn't have been hard to add, but recording and playing
back PCM was probably not in the cards.  Broadcasting your phone voice
to whoever else was in your group, though, would have been doable in
the analog domain.  Per-character color would, I think, have been a
poor tradeoff, but maybe per-line color would have been adequate.

A light pen would have needed only about microsecond resolution to
identify a given letter on the display, but it isn't entirely clear
how it would go about transmitting this information over a regular
phone line.  If there was a way to feed it the front and back porches
from the NTSC signal, there might be some hope, but otherwise it seems
like whatever internal timing reference it had would drift hopelessly.
(This was before the quartz watch revolution.)  Encoding its position
in a pair of audible tones would not be unreasonable.  Nowadays, of
course, the whole prospect of a light pen is hopeless with LCD panels.
A tone-generating mouse, however, would be entirely usable, both then
and now.

### Modern AVRs ###

You could probably build something like this today with an ATMega328
(about 20 times the speed of a Nova but with only 8 KiB of RAM) and
the Arduino TVout library for a group of five displays.  You could use
an analog demultiplexer chip and some 10MHz op-amps (these exist now,
though maybe not in 1972) as buffers to put
each line onto the right output video signal, and probably bitbang the
PS/2 protocol on five keyboards, although it might be hard to meet the
PS/2 deadlines when you're stuck in a timer interrupt handler for most
of the 64 microseconds.

Slower scanning
---------------

Suppose you could use a long-persistence phosphor like the ones
conventionally used on analog oscilloscopes, and commonly used on
computer terminals at the time (which is why the screens were green.)
(This would also make light pens impossible.)  Then you wouldn't have
to repaint the screen thirty times a second; you could repaint it,
say, every two seconds, even without exotic and finicky direct-view
bistable storage tubes (DVBSTs) like the Tek 4014 used.

If you try to apply this in a simple way, by sharing the character
generator circuitry and ROM between more screens, it doesn't really
help, because the main cost of the system described above is really
the RAM.  But we can use it instead to reduce the amount of RAM needed
and increase the system's flexibility, because we don't need special
video RAM to feed the character generator at reliably high speeds; we
can generate scan lines, vector paths, or at least text lines on the
fly from application data.  If the computer system runs at 200,000
instructions per second and can devote half of this to generating
video signals, and we need to repaint every two seconds, then we only
have about 6,300 instructions available per screen repaint (if we are
generating 32 channels).

At such a low speed, perhaps the best we could do would be to use a
character generator that reads from a specified position in main
memory.  If we shoot for 12 lines of 80 5x8 characters, like a VT50,
per two-second screen, but continue with the 64 microsecond line scan
time, then our single character generator can drive 325 slow screens,
which greatly exceeds the memory capacity of the computer to do
anything useful with.

Suppose instead we shoot for 1-second updates of 32 24-line screens.
That's 6144 total scan lines, one every 0.163 ms; once every 8 scan
lines (1.3 ms, 260 instructions) we need to update the character
generator's line-start pointer.  That's still probably too much load
on such a slow computer as the original Nova, but it's within the
bounds of plausibility.  It would be straightforwardly achievable on
the 300-ns-cycle 1970 SuperNova if using SRAM instead of core.

Memory access contention might be another issue: if the character
generator doesn't have its own internal buffer for one line of bytes,
it has to read a character from main memory every 5 pixels, generating
8x as much memory traffic.  If it only reads 80 bytes every 1.3 ms, at
300 ns per 16-bit word (which I said you probably need anyway) it
would need to use memory for 12 microseconds out of the 1300 to read
them, and even with 1200 ns core it would only need 48 microseconds.
Without the internal buffer these numbers go up to 96 microseconds and
384 microseconds respectively, the second one amounting to about a
third of the total memory bandwidth and thus having a significant,
highly undesirable impact on the CPU's speed.  Moreover, it would also
need strong guarantees of timeliness — it wouldn't be able to tolerate
any extra memory latency, so it would need to have priority over the
CPU.  The 80 bytes of memory would cost about US$39 if they cost the
same as the core memory add-on for the Nova described earlier, but
probably in practice you'd have to use semiconductor memory, which
would cost a few times more.  This would clearly be a good tradeoff
for 7% of the whole computer's performance.

It's probably worthwhile actually to stick the whole array of
line-start pointers in main memory instead of trying to update a
character generator register from an interrupt handler thousands of
times a second.  There are 768 of them, which would amount to 1536
bytes if they were in an array, some 1% of all of RAM, which is
reasonable.  (If all of the monitors had unique text on all of their
lines, we could dispense with the pointers, but that would be 61
kilobytes, 47% of RAM.  So it's probably necessary to have some degree
of sharing in order to free up space for application data; the array
of pointers is the easiest way to do this.  This could be as simple as
some blank lines.)

The modern inversion
--------------------

So much for 1972.  Now it's 2020, 48 years later, and TS-80P soldering
irons routinely have STM32F microcontrollers in them: 48–72 MHz, a
32-bit parallel ALU, RISC with nearly one instruction per clock,
32-128 KiB of Flash, maybe 20 KiB of RAM, hardware multiply, hardware
floating point in some cases, 1500 pJ per instruction; maybe US$2 in
quantity 1.  That's about the same amount of Flash as the Nova's
typical RAM, plus a somewhat smaller additional amount of RAM.  What
can we do with that?

Well, there's no need to use character generators, that's for sure.
You can bitbang NTSC no problem: a 64-microsecond scan line is
2000–5000 32-bit instructions instead of, like, 13 16-bit
instructions.  You can bitbang *color* NTSC, which is beyond the
capacity of an AVR.  You can bitbang multiple NTSC composite signals
in parallel.

If we crudely estimate that US$180 per user in 1972 is equivalent to
about US$3600 today — reasonable based on gold and petroleum prices,
though the CPI would suggest more like US$1800 — then we can afford
some 1000–2000 microcontrollers per user, tens of megabytes of SRAM,
tens of billions of operations per second.  You could reasonably
dedicate a microcontroller per scan line on an NTSC or even megapixel
screen, if that would be a helpful thing to do, which it probably
isn't.

Probably a more useful approach is, instead of only interfacing to
humans through the physical objects that are easiest to interface
through, such as televisions, to attempt to interface though objects
that are more difficult, compensating for the difficulty to some
extent through software.  This involves using control systems with the
available actuators to structure the objects so they are usable as
further transducers.  Digital fabrication, including both shaping
processes (subtractive, additive, deformation) and assembly processes
(welding, soldering, screwing), enables the creation of objects with
enormously more transducers than the simple vacuum tube that is a 1972
television.

Computation and control have become cheap; we need to leverage that
into cheap actuation and cheap sensing.
