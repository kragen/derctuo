Due to skywave propagation, hams using MF and HF radio routinely
communicate 1000 km or more with transmit powers on the order of one
watt; under exceptional conditions, transmissions of 1000 km on 1 mW
of transmitted power have been reported.  Typical transmission modes
include (very slow) CW and the WSJT modes, many of which are around
one bit per second.

Directional transmission at MF (300 kHz to 3 MHz) and HF (3 to 30 MHz)
would seem to require impractically large antennas: even 30 MHz is 10
meters, and 300 kHz is 1 km.  However, phased-array transmission and
reception from an antenna array distributed over a significant
geographical area should be possible, and with practical numbers of
transceivers (10 to 1000 transceivers) significant degrees of
directionality should be possible; without understanding the math, I'm
guessing it would be 10 to 30 dBi, with the additional advantage (for
skywave propagation) that most of the energy would propagate
horizontally.  (My intuitive reasoning is that in the direction of the
wave, all 1000 transmitters are in phase, so the amplitude is 1000
times higher than the wave from a single transmitter, while in other
directions, it's only 32 times higher, so it's 32 times higher in the
direction of transmission, which means 1000 times higher power.)

The little FM radio transmitters you might plug into your MP3 player,
legal since 2006 in the EU and longer in the US and Canada, are only
about a microwatt, 10 nW in the US, 50 nW in the UK, 25 microwatts in
Japan.  A Wi-Fi card might emit 200 milliwatts.  So I think 10
milliwatts per transmitter site ought to be reasonable.

Modern impulse radio or ultrawideband techniques should be able to
essentially eliminate interference with the orthogonal signals
conventionally used.  A commercial AM radio station, for example,
might transmit at 10 to 100 kW over a bandwidth of 20 kHz, on the
order of 1 W/Hz.  A 10mW impulse radio whose pulses are evenly spread
across the whole medium-wave AM broadcast band from 526.5 kHz to
1606.5 kHz would average 9 nW/Hz, five or six orders of magnitude
quieter, easily below the noise floor, although it might become
(faintly) audible if it were 30 dB higher in a particular direction.

This 1080 kHz bandwidth gives a temporal precision of about a
microsecond, suggesting a few hundred kilobits per second of possible
transmission speed.

Transmitting over the shortwave band from 2.3 to 26.1 MHz would permit
multi-megabit transmissions, though of course subject to ionospheric
conditions; there used to be 500-kW Voice of America broadcasting on
this band, though I'm not sure there still is, but [Wikipedia tells
me][0] there are 1200-kilowatt shortwave broadcasters, and I think
their bandwidth may be 10 kHz.

[0]: https://en.wikipedia.org/wiki/International_broadcasting

Chirping the transmitted pulses, like LoRa or chirped radar, would
avoid the need for high peak-to-average power ratios that might
otherwise pose a difficulty, and would also reduce the time-domain
artifacts that would otherwise appear to unintentional wideband
receivers.

(Commercial FM radio typically also transmits at a few tens of kW, but
it's in the 87-105 MHz range, where there's no significant ionosphere
propagation.)

Running transceivers on harvested RF energy may permit embedding them
in concrete, under ground, or hanging them from trees.  But it
probably would not permit average transmitted power of 10 milliwatts
or more; 100 microwatts might be more reasonable.

Lower-duty-cycle communication might reduce the degree of interference
with other systems, and would surely reduce the energy transmitted per
bit.  As I understand it, there's no floor on energy transmitted per
bit with a given noise floor, if you transmit slowly enough.  If
you're doing pulse-position modulation with 100-nanosecond timeslots,
then you can transmit one bit in 2 timeslots, two bits in 4 timeslots,
three bits in 8 timeslots, etc.; at some point your timing
synchronization between the transmitter and receiver will start to
suffer, but a regular quartz crystal has drift of about 10 ppm, while
a temperature-compensated crystal oscillator (TCXO) is typically
around 1 ppm.  So you could imagine, for example, transmitting one
pulse every 65536 timeslots (6.55 ms) to represent a 16-bit symbol.
To get the same error probability per symbol, you'd need to send it at
a higher amplitude than if you were sending one pulse every other
timeslot, but I think only something like 6 times higher, assuming
AWGN.  (XXX make this rigorous, or at least do some experiments)

If that's correct, you get about 5x the energy efficiency per bit by
using such a low-duty-cycle system, but you transmit 4096 times
slower.  However, it might increase interference with existing
licensed uses of the spectrum, for example introducing more audible
impulsive noise into AM radio.

How would you coordinate the radio transceivers to transmit data?
It's a bit like the firing-squad problem in cellular automata; they
can use lower-power, higher-bandwidth, higher-frequency local radio
among themselves to compute precise relative geolocations, synchronize
their clocks, and buffer up bits to be sent in a phased-array fashion,
or after being received in a phased-array fashion.  They could use,
for example, the 1800 MHz GSM spectrum, or the 2.4 GHz unlicensed
spectrum.  Time-domain signaling across a GHz of bandwidth should
permit baseline measurements with a precision of a few centimeters.

Passive reflection by disconnecting an energy-harvesting antenna might
be the most efficient way to produce pulses, and might also be more
regulatorily acceptable.  In urban areas, energy-harvesting
researchers have found 1 to 100 microwatts per square centimeter in
each of several different bands, including AM radio, digital TV, and
especially the GSM and 3G bands.  A simple calculation suggests that
an MF AM radio loop antenna enclosing 10 m^2 at 2 km from a 50 kW
broadcasting station intercepts about 10 m^2 50 kW / 4 pi (2 km)^2 =
10 mW, although probably in practice the number is somewhat larger.
Such an antenna might be illuminated by several such stations.  By
selectively making the antenna open-circuit at certain moments, those
10 mW will be reflected instead of absorbed at those moments, across
all the frequencies that efficiently couple to the antenna.

Such passive reflection avoids the necessity to convert RF energy to
stored voltage and then back again, with its attendant losses of
probably some 20 dB, and since it does not transmit any energy, it
might avoid regulatory entanglements; moreover it will not produce any
energy on any frequencies that are not already in use.  However, it
makes it impossible to harvest energy on one band (such as GSM) and
transmit it on another, and it makes chirping impossible.
