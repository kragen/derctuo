I've previously written about [ultraslow radio for decentralized
global digital
communication](https://www.mail-archive.com/kragen-tol@canonical.org/msg00303.html),
but since then I've read a bit more about the topic, including a
little bit of the ample literature on amateur radio DX, QRP, and
contesting.

Due to skywave propagation, hams using MF and HF radio routinely
[communicate 1000 km or more with transmit powers on the order of one
watt][1] (there's a "thousand-miles-per-watt" award);
under exceptional conditions, transmissions of 1000 km on 1 mW
of transmitted power have been reported.  Typical transmission modes
include (very slow "QRSS") CW and the WSJT modes, many of which are around
one bit per second.

[1]: https://en.wikipedia.org/wiki/QRP_operation

So now I see how to build infrastructure that permits
global data communication at hundreds of kilobits per second when the
ionosphere is favorable, without emitting a noticeable amount of radio
interference, and without requiring more power than is easily
available by energy harvesting.  A global network of low-power
kilometer-scale phased arrays can
speak ultrawideband MF and HF to each other, but ultrawideband at
higher frequencies internally and to nearby mobile radios.

Power levels
------------

A Wi-Fi card might emit 200 milliwatts, although
the little FM radio transmitters you might plug into your MP3 player,
legal since 2006 in the EU and longer in the US and Canada, are only
about a microwatt, 10 nW in the US, 50 nW in the UK, 25 microwatts in
Japan.  [The US allows 100 mW unlicensed narrowband AM radio transmitters][3], so
I think 10 milliwatts per transmitter site ought to be reasonable.

[3]: https://en.wikipedia.org/wiki/DBm

In a memory-holed YouTube video,
Naomi Wu recently reviewed the Ulefone Armor 3WT FRS cellphone, which
includes a 2W FRS walkie-talkie.  She
reports that in Shenzhen she can get several blocks of range, which is to say,
several hundred meters.  FRS and
GMRS radios commonly transmit at such powers; [GMRS] is permitted up
to 50 watts, though WP says 1-5 watts is more common in practice, and
FRS in the US was limited to 500 mW until 2017; FRS commonly gets a
kilometer or so of range, though (again, WP says) tens of kilometers
are possible "under exceptional conditions...such as hilltop to
hilltop".  3G mobile phones also transmit 2 W.  So if there's no
regulatory or interference problem, it's reasonable for even a
handheld device to transmit at 1-2 watts.  (Most cellphones are, I
think, up to 1 watt.)

[GMRS]: https://en.wikipedia.org/wiki/GMRS

Handheld ferrite loopstick antennas are capable of transmitting and
receiving MF signals like those used for AM radio, but their antenna
efficiency is fairly low.  A better approach
for mobile stations is probably to use higher
frequencies to connect handheld devices to large, fixed infrastructure
like a long-distance phased array, which then handles the long-range
communication.  Still, these short-range links might be able to reach
many kilometers.  (LoRa at 915 MHz can reach 10 km in rural areas,
though fewer km in cities; one-watt GSM cellphones can talk to a base
station 35 km away, and a "timing advance limit" has been hacked into
some GSM equipment to extend that range further.)

A handheld device is inevitably a point source of interference, with
the unavoidable inverse-square interference pattern that implies.  A
kilometer-scale phased array is, by contrast, a diffuse source, so it
can emit at a much higher power before it starts to become a nuisance
to neighbors.

GPS
---

GPS receivers cost a few dollars and receive signals at -125 dBm or
less; some can lock in a signal at -142 dBm, which is quite impressive
considering that the thermal noise on a 2-MHz-wide GPS channel is
about -111 dBm.  They are made cheaper by the fact that they run at
over 1 GHz, so they don't need large antennas.  Acquiring these
signals is feasible because they are perfectly uncorrelated over long
periods of time, like an LFSR.  Ultrawideband techniques have the same
virtue.

Ultrawideband and frequency bands
---------------------------------

Modern impulse radio ("ultrawideband") should be able to
essentially eliminate interference with the nearly orthogonal narrowband signals
conventionally used.  A commercial AM radio station, for example,
might transmit at 10 to 100 kW over a bandwidth of 20 kHz, on the
order of 1 W/Hz.  A 10mW impulse radio whose pulses are evenly spread
across the whole medium-wave AM broadcast band from 526.5 kHz to
1606.5 kHz would average 9 nW/Hz, eight orders of magnitude
quieter, easily below the noise floor, although it might become
(faintly) audible if it were 30 dB higher in a particular compass
direction because of (see below) phased-array directional
transmission.

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

(Commercial FM radio typically also transmits at a few tens of kW, but
it's in the 87-105 MHz range, where there's no significant ionosphere
propagation.)

### Chirping and wider bands ###

Chirping the transmitted pulses, like LoRa or chirped radar, would
avoid the need for high peak-to-average power ratios that might
otherwise pose a difficulty, and would also reduce the time-domain
artifacts that would otherwise appear to unintentional wideband
receivers.  Straightforward chirping wouldn't help to avoid narrowband
receivers, though; if you were to chirp from 526.5 kHz up to 1606.5
kHz in 1.08 milliseconds, you're only chirping 1 kHz per microsecond,
so you only spend 20 microseconds in each 20-kHz-wide AM station.
This would only attenuate the part of the impulsive noise added to AM
above 50 kHz, which the humans can't hear anyway.

You could imagine doing several simultaneous chirps, though, which
might help more; one that sweeps from 526.5 kHz up to 548.1 kHz over
that millisecond, while another sweeps from 548.1 kHz up to 569.7 kHz,
and so on.  Effectively each chirp would be a single AM station wide,
and spread over the whole millisecond, thus strongly attenuating the
parts of the impulse above about 1 kHz, making it considerably less
audible.  Presumably this waveform still retains the time-domain
precision deriving from its >1MHz bandwidth.

A more effective way to reduce interference might be simply spreading
the signal over a wider bandwidth by using shorter pulses.  If the
pulses were 30 ns instead of 1000 ns, for example, going up to 33 MHz
instead of 1.5 MHz, you'd have 15 dB
less power in any given station's 20 kHz band, 0.3 nW/Hz, about 95 dB
quieter than AM broadcasters --- 63 dB because of transmitting at
63 dB lower power, plus 32 dB because it's spread across 17000 times
as much bandwidth.

Phased-array transceivers
-------------------------

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

How would you coordinate a phased array of radio transceivers to transmit data?
It's a bit like the firing-squad problem in cellular automata; they
can use lower-power, higher-bandwidth, higher-frequency local radio
among themselves to compute precise relative geolocations, synchronize
their clocks, and buffer up bits to be sent in a phased-array fashion,
or after being received in a phased-array fashion.  They could use,
for example, the 1800 MHz GSM spectrum, or the 2.4 GHz unlicensed
spectrum.  Time-domain signaling across a GHz of bandwidth should
permit baseline measurements with a precision of a few centimeters.

Of course the same phased-array correlation approach can be used for
reception.  Probably MIMO techniques to augment bandwidth are not
directly applicable over such long distances due to diffraction.

However, such a phased array could easily transmit to several
destinations at once, or receive from several senders at once.  If
there are multiple relay stations available, it may be possible to
augment the point-to-point bandwidth between two phased arrays by
relaying the information in parallel over geographically diverse
routes, like Ethernet channel bonding.

### Diffraction ###

For the diffraction limit to be better than 30 dBi, so the phased
array is limited by the number of transmitters rather than the
aperture, the diffraction beam divergence needs to be less than 4
pi/1000 steradians, very crudely, which I think means less than about
110 milliradians, 6 degrees.  Suppose we're using 1.220λ/D, the Airy
limit for a circular aperture, as an approximation, and we use 1 MHz
for λ: 300 m.  So we want 1.220 300 m/D = 0.11, so D = 1.220 300 m /
0.11 = 3.3 km, like, a transmitter every 100 m.  Or 10 km if we want
to get all the way down to 300 kHz.  Normally we'd worry about
sidelobes from spreading the transmitters too far apart, but I think
that problem disappears with ultrawideband signals, since the
sidebands for all the different frequencies are in different places.

However, if the transceivers are all on the ground, which is nearly
planar, we're still going to have massive diffraction in the vertical
direction, as our energy is spread across 30 degrees or more, even
after half of it is reflected from the ground.

If your energy is spread evenly over 6 degrees, then after traveling a
quarter of the way around Earth, what is left of it will be spread
over some 700 km of width; this is perhaps 200 times the distance it
was spread over originally, if the original phased array was 3.3 km,
and of course it is also spread out vertically in a nonuniform way
between the surface and the ionosphere.  200 times is a surprisingly
modest -23 dB, although of course that's not the attenuation from the
transmitter; it's the attenuation from the open spaces in the tens of
meters between the transmitters to the place a quarter of the way
around the world.

It might be necessary to confine the beam to a narrower horizontal
angle than 6 degrees to compensate for the unavoidable vertical
spread.

Energy harvesting
-----------------

Running transceivers on harvested RF energy may permit embedding them
in concrete or underground, or hanging them from trees.  But it
probably would not permit average transmitted power of 10 milliwatts
or more; 100 microwatts might be more reasonable.

### Passive reflection instead of transmission ###

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
transmit it on another, and it makes chirping impossible.  For
communication on higher frequencies, antenna directivity might also be
relevant; your antenna system might reasonably be organized to reflect
the incoming illumination toward the destination.

### Harvested solar energy ###

Worth noting is that 10 milliwatts of full sunlight is 0.1
cm<sup>2</sup>, or about 0.7 cm<sup>2</sup> of a commonplace solar
cell.  So even a few square centimeters of PV cells would provide much
more power *on average* than all this RF energy-harvesting stuff, even
in areas brightly illuminated by cellphone towers.  They might be able
to produce alternating magnetic fields that transfer power wirelessly
to a larger, less visible transceiver, perhaps embedded in a wall.

Low-duty-cycle communication
----------------------------

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

Low-duty-cycle communication has an interesting relationship with
chirping, since the effect of chirping is precisely to extend the duty
cycle.  On one hand, if the underlying signal you're trying to
transmit isn't low-duty-cycle, chirping it won't do any good --- your
chirps will overlap, and so you won't get the PAPR improvement you
normally get from chirping.  On the other hand, that PAPR is precisely
what allows you to leave your radio turned off most of the time and
save power, so if you "improve" it too far, you will exceed your power
budget.

Encoding
--------

Of course you want to use error-correction coding so that no one pulse
is strong enough to be received clearly at the destination; you want
the pulses to be tens of dB below the noise floor so that substantial
coding gain is needed to detect them, even near the source.  The best
way to ensure non-interference is non-detectability.

Estimating potential results at 1-100 megabaud
----------------------------------------------

It's already commonplace for QRP hams to reach 1 bit per second
transmitting 1000 km on 1 watt.  Conservatively, phased-array
transmission should buy you 20 dB, while phased-array reception should
buy you another 20 dB.  Supposing that those hams are not in the
bandwidth-limited regime of the Shannon limit, using ultrawideband may
not buy you any extra bandwidth, just keep you from slamming into a
narrowband bandwidth ceiling.  1000 transmitters at 10 mW each works
out to 10 watts rather than 1 watt, giving you another 10 dB, for a
total of 50 dB, or 100 kilobaud, per phased-array-to-phased-array
link.  If you can talk to ten phased arrays at once, that should give
you a megabaud.  But if the phased arrays miraculously work out to buy
you 30 dB instead of 20, you'd have 100 megabaud.

Alternative communication media
-------------------------------

Earth-moon-earth or "moonbounce" communication is already commonplace
among hams and sometimes is high enough bandwidth to hold voice
conversations over.  Doing the equivalent using passive MEO satellites
would require more precise and dynamic tracking, to the point that
it's probably only practical at microwave frequencies, but would
suffer the d<sup>4</sup> loss of the moonbounce path over a much
shorter distance, and still would cover most of a terrestrial
hemisphere.  LEO satellites have an even shorter path loss and larger
cross-section, but only cover a thousand km or so.  Meteor-trail
communication is an existing well-known technique for high-bandwidth
opportunistic communication at a similar range.  And the ocean's SOFAR
channel, though it has only a few kHz of bandwidth, has better
attenuation characteristics, more consistency, and lower noise than
the ionosphere route.
