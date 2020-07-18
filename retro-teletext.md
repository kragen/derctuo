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
