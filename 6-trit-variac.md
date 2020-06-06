Watching the YouTube channel of Espacio de César, I was amused to see
him describe a "homemade 8-bit variac" ("variac casero de 8 bits").
He suggests winding 8 secondaries of different sizes on a single
transformer whose primary is connected to 240 VAC: one that produces 1
VAC, one that produces 2 VAC, and so on up to 128 VAC.  (He's using a
microwave-oven transformer, but recommends using a smaller one
instead.)  By connecting these to 8 pairs of banana-plug terminals in
a metal box, you get a sort of variac; for example, if you want 42
volts, you can put in series the 2-VAC, the 8-VAC, and the 32-VAC
winding with two jumper wires.

But there are other ways you can get 42 volts; for example, you can
use the 32-VAC winding in series with the 16-VAC winding, then wire up
the 4-VAC and 2-VAC windings *backwards* in series with that.

Balanced ternary gets you to 364 VAC in 1-volt increments in only 6 secondary windings
--------------------------------------------------------------------------------------

This suggests instead using balanced ternary.  With a 1 VAC winding
and a 3 VAC winding, you can get 1 VAC, 2 VAC (by wiring the two
windings in series in opposition), 3 VAC, or 4 VAC (by wiring them in
series).  By adding 9 VAC, 27 VAC, 81 VAC, and 243 VAC windings, you
can reach any voltage up to 364 VAC in 1-VAC steps, and this is the
minimal number of windings you need to reach it.

Multitap secondary windings can deliver even more voltages with less terminals
------------------------------------------------------------------------------

That requires 12 banana-plug terminals, though.  If you want to
minimize the number of terminals rather than the number of windings,
you might be able to do better with center-tapped windings.

For example, if you have one winding with three terminals whose two
segments are 1 V and 2 V, you get 1, 2, and 3 VAC with three
terminals; a second winding with three terminals whose two segments
are 7 and 14 volts gives you all voltages from 1 to 24 volts AC; a
third winding of 49 and 98 volts gives you all voltages from 1 to 171
VAC.  That's 9 terminals; a fourth center-tapped winding, with 343 and
686 volts in its segments, bringing us to 12 terminals as before,
might then bring us from 1 to 1200 volts AC in one-volt steps.  Or we
could use a fourth 343-volt winding with no center-tap and get up to
514 volts with only 11 terminals rather than the 12 required by the
balanced-ternary scheme to reach 364.

But what if we have *four* terminals on a winding?  You could have,
for example, a winding with a 1-VAC segment, a 3-VAC segment, and a
2-VAC segment, in that order; this gives you 1, 2, 3, 4, 5, and 6
volts between its six different pairs of terminals.  A second
four-terminal winding with 13, 39, and 26 volts on its segments gets
us 1-84 volts.  A third winding with 169, 507, and 338 volts on its
segments gets us 1-1098 volts, with the same 12 terminals that would
give us 1-64 volts with César's binary scheme, 1-364 volts with the
balanced-ternary scheme, or 1-1200 volts with the single-center-tapped
scheme.

So it seems like the single-center-tapped scheme is optimal, at least
to minimize the number of voltages you can get for a given number of
terminals.  The double-center-tapped scheme is very nearly as good,
though, and it uses less jumper wires: you can reach any voltage up to
1098 volts with only two jumpers instead of the three you might need
with the single center-tap.

One-volt precision is maybe more important when you're at 2 or 3 volts
than when you're at 950 volts, so it would be nice if we could
separate the voltage levels a bit more at higher voltages;
unfortunately, the voltages on the various secondary windings do sum
linearly, so you can't avoid this completely.  But if you have one
winding with segments of 1, 3, and 2 V and a second one with segments
of 15 and 30 V, then you can do any one-volt voltage from 1-6 volts,
9-21 volts, 24-36 volts, and 39-51 volts, with just seven terminals
and a single jumper.

    subs = lambda items: set(sum(items[i:j])
                             for j in range(len(items)+1)
                             for i in range(j))
    combos = lambda subses: {0} if not subses else set(a+b
        for c in subses[0] for a in [c, 0, -c] for b in combos(subses[1:]))
    combos([subs([1]), subs([3]), subs([9]), subs([27]), subs([81]), subs([243])]
        ) == set(range(-364, 365))
    combos([subs([1, 2]), subs([7, 14]), subs([49, 98]), subs([343, 686])]
        ) == set(range(-1200, 1201))
    combos([subs([1, 3, 2]), subs([13, 39, 26]), subs([169, 507, 338])]
        ) == set(range(-1098, 1099))

I don't think we can do better by connecting triples of windings
together in a Y configuration, like some BLDC motors, because the
1-3-2 setup already gives us six distinct voltages for the six
distinct pairs of terminals, and they cover a contiguous range of
integers.

A practical configuration
-------------------------

I think that, if you were going to do this in real life, the most
practical configuration would use a single high-voltage winding with
two terminals and two low-voltage windings with four terminals each,
with a first winding of segments of ½, 1½, and 1 volt and a second
winding of segments of 8, 24, and 16 volts.  This gives you 0.5-volt
resolution for 0-3 volts, 5-11 volts, and 13-19 volts, and
2-volt-or-better resolution up to 51 volts, all configured with a
single jumper.  This is not enough to kill you unless you are
astonishingly fortunate.

The high-voltage winding might be 120 volts, which in combination with
the low-voltage windings gives you voltages up to 171 volts, with an
18-volt gap between 51 and 69 volts; all of this for ten terminals and
three secondary windings (plus the primary).

Ganging up two transformers
---------------------------

Now, if transistor *cores* are abundant and you just want to keep
*windings* to a minimum, you could get a more favorable spread of high
and low voltages by putting two separate transformers in the box, one
fed from the power line with two to four terminals on its secondary
brought out to the front panel, and a second transformer connected
only to front-panel terminals, perhaps with two windings with three or
four terminals each, either of which can be connected as a "primary"
to the secondary of the first transformer.  One reasonable winding
configuration for the second transformer might be turns numbers of
1n-3n-2n on one winding and 10n-18n on the other.  This affords 18
different stepups as low as 3:5 and as high as 1:28, including 2, 2½,
3, 5, 6, 7, 9, 10, 14, 18, and 28; and of course their reciprocals as
stepdowns.

    import fractions
    ' '.join(str(x) for x in sorted(f for n in subs([1, 3, 2])
                                      for d in subs([10, 18])
                                      for f in [fractions.Fraction(n, d),
                                                fractions.Fraction(d, n)]))

So if you had a center-tapped winding on the primary transformer with
a 14-volt segment and a 134-volt segment, you could get 111 different
voltages out of the combination of the two transformers, ranging from
½ VAC up to 4144 VAC.  The full list is:

    1/2 7/9 1 7/5 3/2 14/9 2 7/3 5/2 14/5 3 28/9 35/9 21/5 14/3 67/14
    37/7 28/5 7 67/9 74/9 42/5 67/7 74/7 67/5 14 201/14 74/5 134/9 111/7
    148/9 134/7 148/7 67/3 70/3 335/14 74/3 185/7 134/5 28 201/7 148/5
    268/9 222/7 296/9 35 335/9 201/5 370/9 42 222/5 134/3 140/3 148/3
    252/5 268/5 296/5 63 196/3 67 70 74 392/5 402/5 84 444/5 98 126 392/3
    134 140 148 196 670/3 740/3 252 268 296 335 370 392 402 444 1340/3
    2412/5 1480/3 2664/5 603 1876/3 666 670 2072/3 740 3752/5 804 4144/5
    888 938 1036 1206 3752/3 1332 1340 4144/3 1480 1876 2072 2412 2664
    3752 4144

    ' '.join(str(x) for x in sorted(set(f*v for n in subs([1, 3, 2])
                                            for d in subs([10, 18])
                                            for v in subs([14, 134])
                                            for f in [fractions.Fraction(n, d),
                                                      fractions.Fraction(d, n),
                                                      1])))

Or, as decimal approximations:


    0.50 0.78 1.00 1.40 1.50 1.56 2.00 2.33 2.50 2.80 3.00 3.11 3.89
    4.20 4.67 4.79 5.29 5.60 7.00 7.44 8.22 8.40 9.57 10.57 13.40
    14.00 14.36 14.80 14.89 15.86 16.44 19.14 21.14 22.33 23.33 23.93
    24.67 26.43 26.80 28.00 28.71 29.60 29.78 31.71 32.89 35.00 37.22
    40.20 41.11 42.00 44.40 44.67 46.67 49.33 50.40 53.60 59.20 63.00
    65.33 67.00 70.00 74.00 78.40 80.40 84.00 88.80 98.00 126.00
    130.67 134.00 140.00 148.00 196.00 223.33 246.67 252.00 268.00
    296.00 335.00 370.00 392.00 402.00 444.00 446.67 482.40 493.33
    532.80 603.00 625.33 666.00 670.00 690.67 740.00 750.40 804.00
    828.80 888.00 938.00 1036.00 1206.00 1250.67 1332.00 1340.00
    1381.33 1480.00 1876.00 2072.00 2412.00 2664.00 3752.00 4144.00

    ' '.join('%.2f' % float(x)
             for x in sorted(set(f*v for n in subs([1, 3, 2])
                                     for d in subs([10, 18])
                                     for v in subs([14, 134])
                                     for f in [fractions.Fraction(n, d),
                                               fractions.Fraction(d, n),
                                               1])))

Note that this still requires only 10 terminals: three on the main
transformer's secondary winding, four on the auxiliary transformer's
low-turns winding, and three on the auxiliary transformer's high-turns
winding.  Like the single-transformer "practical" configuration
described above, it also requires four windings and at most two
jumpers; it can produce fewer distinct voltages (only 111 instead of
153) but they are spaced out in a much more useful fashion: no more
than 0.5 volts apart up to 3.1 volts, no more than 1 V apart up to 5.6
volts, no more than 2 V apart up to 10.6 volts, no more than 4 volts
apart up to 46 volts, and so on.

It should be straightforward to come up with a better set of numbers
for the windings, too, that give even more evenly spaced voltages, and
perhaps at rounder numbers, although that aim seems to be in conflict
with the aim of increasing the number of distinct voltages.

The above ignores the possibility of using the windings on the second
transformer in autotransformer mode, so a larger number of
configurations is actually possible; for example, you could hook up 14
volts to the 10n-turn winding segment and get 25.2 volts off the
18n-turn winding segment, a number which isn't in the above list.
This relies on the primary transformer to provide galvanic isolation,
which ought to be fine.

It's somewhat dubious whether you'd really want to use the higher
voltages on such a gadget; they might need to be insulated to a degree
that would make them impractical for the high currents encountered at
low voltages.

Lightswitch reconfiguration
---------------------------

A lower-hassle way to get such flexibility, with only a *single*
transformer, would be to mechanically switch the mains power between
different primary windings.  Two everyday single-pole double-throw
lightswitches of the type commonly used to wire up hallway lights ---
so that you can turn them on or off from either end of the hallway ---
suffice to select among four of the six possibilities offered by a
primary winding with two center taps, without any possibility of a
short circuit.  If the segments have a winding configuration 1n-1n-2n,
then the four possibilities are 1n, 2n, 3n, and 4n; if instead they
are 7n-1n-56n, then the four possibilities are 1n, 8n, 57n, and 64n.

This possibility of 1n, 8n, 57n, and 64n turns on the primary could be
seen as a selectable multiplier of the secondary voltage: respectively
64, 8, 64/57 (about 1.12), and 1.  Suppose that when the primary side
is set to 8x, the medium voltage, the secondary side is like the
low-voltage setup described above under "A practical configuration": a
first winding of segments of ½, 1½, and 1 volts and a second winding
of segments of 8, 24, and 16 volts.  This gives you ½-volt resolution
for 0-3 volts, 5-11 volts, and 13-19 volts, and 2-volt-or-better
resolution up to 51 volts, all configured with a single jumper.
Setting the primary side to 64/57 gets you roughly the same set of low
voltages boosted by about 10%.  But setting the primary side to 1x,
the same secondary-side configurations give you 62.5-millivolt
resolution from 0-375 mV, 625 mV-1.375 V, and 1.625-2.375 V, and
¼-volt-or-better resolution up to 6.375 volts.

Or, if you set the primary side to 64x --- connecting only the middle
segment of the primary winding --- you get 4-volt resolution for 0-24
volts, 40-88 volts, and 104-152 volts, and 16-volt-or-better
resolution up to 408 VAC.  Ideally this 64x setting would be protected
somehow so you didn't do it by accident.  There's probably a reason
they don't make power variacs with two sliders...

Since 63 millivolts to 408 volts is an unreasonably large range for a
single apparatus --- 100 watts at 408 volts is only 250 mA, while at
63 millivolts it would be SIXTEEN HUNDRED AMPS --- maybe a better
choice is to use a single four-terminal winding on the secondary side.
It could be wired, say, 2-5-4, which can produce multipliers [2, 4, 5,
7, 9, 11], and windings on the primary side could be configured, say,
5n-2n-11n, providing divisors of 2n, 7n, 13n, and 18n, since 11n and
5n are inaccessible with the two-lightswitch configuration.  This
design is amusingly analogous to a trucker's 4×6 gearshift, except
that truckers' gear ratios are a lot closer together.

If we set the lowest available voltage here to 1 VAC (2 on the
secondary, 18n on the primary), then our 23 available voltages are
1.0, 1.38, 2.0, 2.5, 2.57, 2.77, 3.46, 3.5, 4.5, 4.85, 5.14, 5.5,
6.23, 6.43, 7.62, 9.0 (two ways), 11.57, 14.14, 18.0, 22.5, 31.5,
40.5, 49.5.

    sorted([round(9*v/d, 2) for v in subs([2, 5, 4]) for d in [2, 7, 13, 18]])

This is an entirely reasonable set of voltages for a ghettobotics lab
benchtop power supply, except that they're AC voltages.  If you
rectify these voltages and charge capacitors with them, they get
higher by a factor of 2<sup>½</sup>: 1.41, 1.96, 2.83, 3.54, 3.64,
3.92, 4.9, 4.95, 6.36, 6.85, 7.27, 7.78, 8.81, 9.09, 10.77, 12.73,
12.73, 16.36, 20.0, 25.46, 31.82, 44.55, 57.28, 70.0.

This approach is also a lot more windings-efficient than the approach
of varying only the secondary windings: it never uses less than 11% of
the primary windings nor less than 18% of the secondary windings, so
the transformer never needs to be more than about six times bigger
than the minimal 50Hz transformer for whatever you're doing at the
moment.  By contrast, with windings of 1V, 3V, 9V, and 27V, the
balanced ternary approach is using 2.5% of its secondary windings when
it's outputting 1V.  Normally the primary and secondary windings need
to be about the same size because their cross-sectional areas per turn
vary in nearly exact proportion to their numbers of turns, so at 1 V
it can only carry 1/40 of its maximum power.

What's the actual turns ratio n?  If our input is 240VAC, it's about
26.67: say, 133 turns, 53 turns, and 293 turns in the three segments
of the primary, if the secondary is actually wired with 2 turns, 5
turns, and 4 turns.  If you're winding the transformers by hand, using
an additional stepdown transformer (or two!) would be a great idea,
just so you don't have to thread a wire through your transformer core
over 900 times.  This, though, suggests a return to the approach of
the previous section, wherein each winding gives you an opportunity to
reconfigure.

An 8-lightswitch reconfigurable design with two transformers but only two jacks
-------------------------------------------------------------------------------

So, suppose we have a primary transformer with two center-taps on its
primary hooked to the wall current through two SPDT switches, and the
two center-taps on its secondary allow you to use two more SPDT
switches to select one of four possible parts of the secondary, and
those are connected to the primary of a second transformer via two
*more* SPDT switches to select one of four possible parts of *its*
primary, and on *its* output we have two *more* SPDT switches which
hook up the output socket to it.  No jumper wires and no possibility
of shorting a winding with them.  What does *that* look like?  What
kind of turns ratios can it give us?

I'm tired of designing, so I generated the random configuration ([25,
9, 32], [5, 2, 11], [25, 24, 28], [7, 2, 12]).  That is, the first
transformer has a primary winding with a 25-turn segment, an 9-turn
segment, and a 32-turn segment, and a secondary winding with a 5-turn
segment, a 2-turn segment, and an 11-turn segment; the second
transformer has a 25-24-28 primary and a 7-2-12 secondary.  (Maybe all
the turns numbers are multiplied by some constant such as 1.5 or 2,
since 2 turns might not be enough to couple well to the magnetic
core.)  What possibilities does this offer?

    import random

    def config(m):
        x = range(2, m)
        random.shuffle(x)
        x = sorted(x[:3])
        x[0], x[1] = x[1], x[0]
        return x

    config(20), config(10), config(20), config(10)

I'm tired of calculating too, so I wrote code to calculate.

    spdt = lambda (a, b, c): sorted([b, a+b, b+c, a+b+c])

    ratios = lambda p, s: sorted(set(fractions.Fraction(n, d)
                                     for d in p for n in s))
    ' '.join(str(f) for f in ratios(spdt([25, 9, 32]), spdt([5, 2, 11])))
    ' '.join(str(f) for f in ratios(spdt([25, 24, 28]), spdt([7, 2, 12])))

This gives us the possible voltage ratios for the first transformer
1/33 2/41 1/17 7/66 7/41 13/66 7/34 2/9 3/11 13/41 13/34 18/41 9/17
7/9 13/9 2 and for the second transformer 2/77 1/26 2/49 1/12 9/77
9/52 2/11 9/49 7/26 3/11 2/7 3/8 21/52 3/7 7/12 7/8.  These do indeed
result in 256 different voltages, which range from about 0.2 volts up
to 420 volts:

    rs = sorted(set(240*t1*t2 for t1 in ratios(spdt([25, 9, 32]), 
                                               spdt([5, 2, 11]))
                              for t2 in ratios(spdt([25, 24, 28]),
                                               spdt([7, 2, 12]))))
    min(rs), max(rs), len(rs)

Specifically, the output voltages are 0.189 0.280 0.297 0.304 0.367
0.450 0.478 0.543 0.576 0.606 0.661 0.850 0.976 0.979 1.039 1.064
1.176 1.228 1.259 1.283 1.322 1.336 1.368 1.385 1.576 1.650 1.672
1.700 1.818 1.900 1.929 1.958 1.977 1.983 2.017 2.026 2.051 2.078
2.121 2.129 2.150 2.177 2.383 2.443 2.517 2.567 2.593 2.672 2.727
2.737 2.927 2.937 2.975 3.106 3.117 3.152 3.193 3.300 3.345 3.415
3.529 3.745 3.801 3.850 3.939 4.034 4.053 4.118 4.242 4.301 4.390
4.406 4.444 4.628 4.675 4.728 4.789 4.848 4.887 5.017 5.186 5.294
5.455 5.525 5.701 5.775 6.050 6.234 6.341 6.364 6.829 6.853 6.942
7.092 7.179 7.273 7.450 7.526 7.619 7.647 7.651 8.182 8.235 8.552
8.595 8.683 8.780 8.895 8.984 9.004 9.076 9.231 9.545 9.697 9.796
10.244 10.280 10.588 10.726 10.909 11.032 11.175 11.329 11.707 11.901
12.022 12.315 12.353 12.468 12.727 12.893 13.171 13.303 13.333 13.476
13.506 13.836 13.977 14.118 14.150 14.359 14.545 14.848 14.851 15.238
15.366 15.556 15.882 16.548 16.684 16.855 17.561 17.622 17.727 17.851
18.236 18.462 18.529 18.701 19.091 19.157 19.353 19.592 19.955 20.000
20.260 20.488 20.754 21.176 21.538 21.742 21.818 21.991 22.273 22.857
23.102 23.337 23.902 24.545 24.706 25.027 26.218 26.434 27.576 28.052
28.368 28.537 28.736 28.824 28.889 30.105 30.732 31.111 32.308 32.613
33.939 34.208 34.286 34.412 34.652 35.854 36.303 37.059 38.182 39.328
39.512 40.000 40.519 41.364 42.552 43.235 44.390 45.157 46.667 47.647
50.256 50.909 51.312 53.333 53.529 54.454 56.104 57.273 60.000 61.463
63.030 63.673 66.585 70.000 74.118 75.385 80.000 80.294 83.077 87.273
88.163 92.195 93.333 94.545 99.048 108.889 111.176 129.231 130.000
130.909 137.143 140.000 148.571 163.333 180.000 193.846 202.222
205.714 280.000 303.333 420.000.

    ' '.join('%.3f' % float(f) for f in rs)

This randomly generated configuration is maybe not a super great
design but it's in some sense reasonable.  Half the values are below
12 volts, there are 256 distinct values, the values are mostly only a
couple percent apart in the middle of the range, and the range covers
over three orders of magnitude.  Over most of the range the design has
considerably more precision in the turns ratio than the margin of
error on the mains voltage.

This is kind of overkill, although the transformers are much more
manageable.  Maybe a single SPDT per winding with a single center tap
on each winding and two center taps on the final output would be
adequate: three lightswitches to "select a range" and then four output
terminals to give you six voltages simultaneously, 48 settings in all.