We were talking about level shifters; I asked if a voltage-divider
level shifter from 3V3 to 5V was going to sink current into the 3V3
pin, and cloudevil surprised me!

Passive level shifters
----------------------

    14:06 < cloudevil> xentrac: No, it doesn't have to, depending on setup.                                        |
    14:07 < cloudevil> xentrac: Something like 3.3K from 3.3V out to ground, 1K from 3.3V out to 5V in, and 2K     |
                       from 5V in to 5V.                                                                           |
    14:22 < cloudevil> It is not much good (without great care paid) if you need to do a MHz signal, and level     |
                       shifters may be the right way there.                                                        |

In [Falstad’s circuit simulator][0], that’s:

    $ 1 0.000005 10.20027730826997 50 5 43
    R -48 160 -80 160 0 2 40 1.65 1.65 0 0.5
    r -48 256 -48 160 0 3300
    r 32 160 -48 160 0 1000
    r 32 80 32 160 0 2000
    R 32 80 32 48 0 0 40 5 0 0 0.5
    g -48 256 -48 272 0
    368 32 160 80 160 0 0

[0]: https://www.falstad.com/circuit/circuitjs.html?ctz=CQAgjCAMB0l3BWcMBMcUHYMGZIA4UA2ATmIxAUgpABZsAoAJRAFoa9xCqW8qwuoIFLT7RCSMGKRUYCegCdW7IQkJKO-GSGy5IC7cM3rOWsPH3ZhvAycFo4TG9cu0OWmlWmDZ9AObGUVQCMYT1sQg4XI2sjGXogA

This does indeed work as advertised.  When the 3.3V wave is at 3.3V,
it holds the 3k3 at 1 mA.  Then the 3k voltage divider between there
and the 5V supply divides the 1V7 difference into 570 mV across the 1k
and 1.13 V across the 2k, with the same 570 μA through both, so the
3V3 source only has to source 430 μA.  This pulls the input on the 5-V
chip to a very acceptable 3.87 V.  Then, when the 3V3 I/O pin is
pulled down to ground, you instead have 5V split across the 3k voltage
divider, so the 5V I/O pin is at 1.67 V, which is probably still okay,
though well above TTL’s 0.8V threshold, and precisely at 5V CMOS’s
⅓Vdd threshold.

(It works a little better if you use 470R instead of 1k in the middle.
You don’t get a lot of noise immunity but you do get some, at least
with CMOS thresholds.)

Now, 3V3 is almost precisely 5V CMOS’s ⅔Vdd threshold, so you may be
able to just use a wire.  And going in the other direction, 5V to 3V3,
you can just use a 2:1 voltage divider, when a simple current-limiting
resistor isn’t enough.  (Generally 3V3 input pins, when they aren’t
actually 5V-tolerant, have some specified limit on how much current
they can sink from a higher-voltage place, like half a milliamp or
something.)

I just hadn’t realized that a non-bogus level shifter could be so
simple and passive.

Active and bidirectional level shifters
---------------------------------------

Here’s [a more elaborate circuit shifter on Falstad’s circuit
simulator][1]:

    $ 1 0.000005 10.20027730826997 50 5 43
    R -48 160 -80 160 0 2 40 1.65 1.65 0 0.5
    g 160 240 160 288 0
    t 128 192 80 192 0 1 0.642686555624928 0.6693572978862428 100
    t 192 192 240 192 0 1 -4.2135673793996755 0.02667074272282007 100
    r 128 192 240 48 0 4700
    r 192 192 80 48 0 4700
    w 80 48 80 176 0
    w 80 208 80 240 0
    w 240 208 240 240 0
    w 240 240 160 240 0
    w 160 240 80 240 0
    w 240 48 240 176 0
    r 80 48 80 -16 0 1000
    R 80 -16 80 -48 0 0 40 5 0 0 0.5
    r 240 48 240 -16 0 1000
    R 240 -16 240 -48 0 0 40 5 0 0 0.5
    r -48 160 32 160 0 470
    w 128 192 128 160 0
    w 128 160 32 160 0
    368 240 48 336 48 0 0
    368 -48 160 -48 96 0 0

[1]: https://www.falstad.com/circuit/circuitjs.html?ctz=CQAgjCAMB0l3BWcMBMcUHYMGZIA4UA2ATmIxAUgpABZsAoAJRAFoa9xCqW8qwuoIFLT7RCSMGKRUYCegHNOVFDT4CUeDpHoAXcBvDFhvQ8L5QxNInnEIEhFUYIXCJbAgwoymh1eLJtPTAjUyFVUPM2aBQwd0IcDGJsUlctWCJ4yAwrTw084TA4egAnfQ5g4RUqdkEaDCLSitCTGur67QB3EBaOEzAMQih6LpM0XuVwzrDlfGm5qaq5-gmZYaU50cm1xZrF-sHtUp7u7n5BQqLmExYz69baqmlBWRK53fCbg4DtZkXPubYWge1BkFjkpUB62wBQEbSmYAMTQR5Vha2RUJhq2whA4Ow42GxtCB2mxHEhy1YNWIX20QA

This circuit uses a simple RS latch made out of four resistors and two
bipolar transistors to do level-shifting in a way that has, I think,
some bidirectional potential.  The output on the right generates
0.06 V or 4.24 V according to the state of the latch, and the 3V3
input (connected through a lower-value resistor) is strong enough to
overpower the latch’s state.

Now, the reason I say this has some bidirectional potential is that,
if the 3V3 pin isn’t driving anything (you disconnect the square-wave
source), then you can drive the “output” directly with 0V or 5V, which
is also enough to overpower the latch.  The leftover part is that your
tri-stated 3V3 pin isn't being driven to 3V3; it’s being driven to
just a Vbe.

This can be remedied by [driving the feedback override in the level
shifter through a voltage divider][2]:

    $ 1 0.000005 10.20027730826997 50 5 43
    r -144 -16 -144 128 0 15000
    r -144 128 -144 240 0 22000
    t -144 240 -80 240 0 1 -4.763777148265759 0.10791218667768503 100
    g -80 256 -80 304 0
    r -80 224 32 224 0 33000
    t 32 224 80 224 0 1 0.529525420053551 0.6374376053938159 100
    g 80 240 80 304 0
    r -80 224 -80 -64 0 1000
    R -80 -64 -80 -96 0 0 40 5 0 0 0.5
    R 80 -64 80 -96 0 0 40 5 0 0 0.5
    r 80 -64 80 208 0 1000
    w 80 208 128 208 0
    w 128 208 128 -16 0
    w 128 -16 -144 -16 0
    w 128 208 208 208 0
    s 208 208 208 -64 0 1 true
    R 208 -64 208 -96 0 0 40 5 0 0 0.5
    s 208 208 208 288 0 1 true
    g 208 288 208 304 0
    368 208 208 272 208 0 0
    w -144 128 -208 128 0
    s -208 128 -208 288 0 1 true
    g -208 288 -208 304 0
    368 -208 128 -272 128 0 0
    s -208 128 -208 16 0 1 true
    R -208 16 -208 -32 0 0 40 3.3 0 0 0.5

[2]: https://www.falstad.com/circuit/circuitjs.html?ctz=CQAgjCAMB0l3BWcMBMcUHYMGZIA4UA2ATmIxAUgpABZsAoAJxAFowabWxCuPwU8UcJThNenMAPEgUNKlRRpRAF2myqLPArlCILGtAyFsWDOwKEEGBMSjQwkDMUlg8hQlkJ4axQQ8j0AOasWjIIPJpUuJwBzJEysiDYKAkxSbgqSSmKnKE5unYIKMRFCDRKCNhlBHbGGHRGkJXE2HhgCH6iwXk6odFQYvH58SyEaf4BAEohGmMzrMQ88rRUSMswCPTToaO5GotCVDprh9CbzDtzefi68PQA7iDXflJoggGPkoJv-IJsSw9flwIuxOP8Bp9Xjcfj8AgBnGTQpF-OZUCDKRgAVwApltESjOD8WAdlsdDoV6AiYciZHh3uAQBicUF8bTvjd+gFsF5WTCMNkbvJAWw+F9WD8xfDxTcxSwYXSCkzccE5dCFarBJz6Ny-hKpHL+UChQiNUDTdxFVjcdNzREiclyTpsNBsOSNvQgA

This is basically the same latch circuit as before, but now the 3V3
input is connected to the middle of the feedback resistor, so it sees
a lower voltage.

Of course cloudevil points out that seven discrete components are
probably not cheaper than a level shifter chip!  And I might want to
consider under what conditions it might oscillate.  But mostly I just
thought it was an interesting way to tackle the problem.  And,
amusingly, it’s only one more component than two purely passive
unidirectional level shifters made from three resistors each — but it
contains two transistors, which are both more expensive and often
slower than resistors.

(It's kind of goofy to describe a resistor by itself as "slow" or
"fast"; it's really the whole circuit.  But I did it anyway.)
