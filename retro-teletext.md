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
  be.

But what could it have been?  Is there a plausible way that a 1972
computer system could have provided computation on demand to 128
TV-screen terminals?

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
  and where the cursors are and so forth.

The ROMs and RAMs need to be read very quickly while painting the
screen.  An NTSC frame is 33.37 milliseconds, so each scan line is 64
microseconds, so each of the 200 pixels across the screen is 318 ns.
However, we can transfer five pixels at a time from the ROM, so we
have 1.59 microseconds to do it, and we can pipeline that with the
following read from the RAM.

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
figure on a 