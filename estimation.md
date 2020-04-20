As I was writing file `TODO.md` I was trying to figure out how fast or
slow I actually am at programming and how predictable this is.

Ur-Scheme was 1553 lines of Scheme and mostly took me four weeks and
three days, from February 3 to March 4 of 02008, exactly a
person-month.  David A. Wheeler's "SLOCCount" says it should have
taken 3.8 person-months using the basic COCOMO formula `2.4 *
(KSLOC**1.05)`, so let's suppose that instead of being the famous "10x
programmer" I am a 3.8x programmer, not compared to normal modern
programmers but to whatever losers the COCOMO model was calibrated on.

As another more recent data point, Dercuano's genpdf.py took me 5 days
(0.25 person-months), and it's 550 lines of code, suggesting 1.28
person-months --- a productivity factor of about 5x for me.  5 is
pretty close to 3.8.

I'd like to evaluate StoneKnifeForth's development speed in this way,
but I don't have any reasonable way to do so, since I don't know how
to evaluate either how many lines of code it is or how long it took me
to write.

SLOCCount says that the part of BubbleOS I have written so far should
have taken 17 person-months since it contains 6473 lines of code,
although about 800 of those are the actuarial tables in the death
clock Toki.  In fact, I wrote most of it from October 12, 02018, to
February 22, 02019, which is almost four months; this is also lower
than SLOCCount's estimate by only a factor of about 3-5.

I wrote Dumpulse mostly October 15-17, 02017, about 0.14
person-months.  Checking out the commit
52f10e5a5d22c9ef8f78992cdc95cbdb8ed4ee79 I get 646 lines of code,
nominally 1.52 person-months.  In this case the multiplier is closer
to 10x.  I think this is partly because I'd already been thinking and
talking about how to do it and partly because I was able to stay
pretty focused for three days --- although the git commits cluster
into only four or five hours on each of those days.

I might be able to speed things up by taking advantage of new
programming technology like generative testing, or by choosing
especially conservative and well-understood designs.
