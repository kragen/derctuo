Could you construct universal sequential digital logic with just LEDs?

It's straightforward to use LEDs for diode logic, which can give you
sum-of-products logic, up to monotonicity — you can get any monotonic
logical function that way, but diode logic alone doesn't give you
inversion.

LEDs can function as photodiodes, although not very good photodiodes.
So you could imagine using the light from one LED to switch another
LED.  But it would seem that you can't get any current gain that way:
each charge-carrier pair that gets annihilated in the transmitting LED
produces at most one photon, and then produces at most one
charge-carrier pair in the receiving LED.  And there are losses at
every stage of this process, thanks to non-ideal quantum efficiencies
and the like, so you can't even get to unity gain.

I think there are at least four ways to solve this problem, which sort
of blur into each other.

1. You can get *voltage* gain, because the voltage in the emitting
    LED will be close to its usual forward voltage, say 1.6V, while I
    think the voltage in the receiving LED can be much higher if it's
    back-biased, say 5V.  But it's easy to trade that off for current gain
    by putting LEDs in series.  For example, you can put three 1.6V LEDs
    in series and thus generate three photons per charge carrier.

2. You may be able to increase the number of photons with
   fluorescence, though at the cost of speed.

3. You can use a "regenerative" design using positive feedback, in
   which the back-biased receiving LED is in series with one or more
   forward-biased LEDs which also illuminate it.  This way, most of the
   electrons produce one or more photons on their way to wherever they're
   going, thus allowing another charge carrier pair to spawn in the
   receiving LED.

    (One problem that occurs to me with the above techniques is that
    it's going to be hard to get more irradiance at the photodetector
    junction than, like, in the rest of these diodes that are glued
    together.)

4. You can initiate an avalanche discharge in the receiving
    LED and directly get current amplification after all, similar to how
    SPADs work.  Like, if you're close to the diode's reverse avalanche
    voltage, maybe you can reduce that voltage threshold by varying
    irradiation, and thus get both voltage gain and current gain.

5. You can get amplification through a bridge configuration.  As long
   as you don't exceed the reverse breakdown voltage, an LED works as
   a (not very sensitive) differential voltage detector.  However,
   this still suffers from a lack of current gain.

6. You can use LEDs as if they were PIN diodes to switch RF signals by
    changing their capacitance with a DC bias, providing enormous
    current gain (like a JFET, leakage current down in the femtoamp
    range controlling an RF current up in the milliamp range) despite
    below-unity voltage gain.  But then how do you rectify the RF
    signal?  A faster LED, I suppose.

[Apparently a 5-mm red LED can generate over 20 μA as a photovoltaic
diode in full sunlight][0], while 1N4148 diodes only generate about
10 nA.  Assuming a 19.6 mm² area and 1000 W/m², the total solar power
incident on the diode is 19.6 mW, which would be 12.3 mA at 1.6 V.  So
that's an efficiency of about 0.16%, compared with 16% for common
low-cost photovoltaic cells.

Typically [LEDs work better to detect slightly shorter-wavelength
light][1], which is a major reason the red LED has such poor
efficiency in sunlight.  So that 0.16% might really be a quantum
efficiency on the order of 2% or so in the right wavelength band.

[0]: https://www.analog.com/en/analog-dialogue/raqs/raq-issue-108.html "LEDs are Photodiodes Too, by James Bryant"
[1]: https://www.electronicdesign.com/markets/lighting/article/21777096/single-led-takes-on-both-lightemitting-and-detecting-duties

These data make me think that getting even a current "gain" of 0.1 is
going to be quite difficult with the first three approaches, much less
getting a current gain above 1.0.  The situation can be improved
somewhat by heating up the LEDs you want to have lower bandgaps and
cooling down the ones you want to have larger bandgaps, but maybe not
to the point where those approaches are feasible.
