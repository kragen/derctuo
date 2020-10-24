How precisely can you measure position?  How precise can a servo be?
What if it's bodged together from mass-market junk?

It's practical to illuminate a fused-quartz optical scale with
365-nm-wavelength LEDs.  A modern CMOS imaging sensor can fairly
easily reach the Abbe limit; if we take our numerical aperture as 1.4,
the Abbe limit is λ/2.8; at 365 nm that's 130 nm.  We can reasonably
expect to estimate the centers of blobs in the image to within about a
quarter of the resolvable feature size, so we should be able to
estimate our absolute position along the scale to within about 35 nm
by this method.

This is only about an order of magnitude worse than the recently best
available optical position sensors, such as Renishaw's 5-nm-resolution
"RELM" scales that limited the precision of [Awtar and Parmar's XY
HiPER NAP from 2008][1], and it should be achievable with cheap
off-the-shelf parts.  The EPSRC Center for Innovative Manufacturing in
Ultra Precision at Cranfield University routinely achieves better
precision than this, 10 nm in many cases and down to 1 nm in some
cases, but I don't know how.

[1]: http://psdl.engin.umich.edu/pdf/J16.pdf "Design of a Large Range XY Nanopositioning System, no date given"

Fused quartz's linear thermal coefficient of expansion is about
0.5 ppm/K, so a meter-long fused-quartz scale will lengthen or shorten
by 35 nm every time the temperature changes by about 70 millikelvins.
I don't think infrared thermometers can measure temperature with this
precision, so *compensating for* temperature variations would probably
require the use of some kind of contact thermometer; I've argued in
[my note on Thermistors](thermistors.md) that this level of
temperature measurement precision is fairly easily achievable without
super-precision circuitry.  Note that precise temperature
*calibration* is not necessary, but the absence of *drift* is.

But contact thermometers are slow, at least the macroscopic ones I was
considering in that note, so it seems likely that a more viable
approach is to *prevent* temperature variations of more than 70 mK or
so.  To do this, you would use PID thermostatic control of a stream of
coolant, such as water, air, or hydrogen, continuously pumped past the
fused-quartz scale to prevent thermal variation; and you can calibrate
the impulse response of the scale's thermal expansion to the error
signal from the PID control system, so you can to some extent
compensate for smaller variations.

500-millikelvin-precision temperature control has been a sine qua non
for precision manufacturing since Michelson's first grating engines.

A different and possibly more difficult problem is that, if you're
measuring motion along such a scale, you may actually be interested in
the relative positions of things attached to the scale and the sensor.
And those things will almost certainly have a much larger TCE than
fused quartz, as well as being subject to some side loading that
causes them to deform elastically.  It may be feasible to use the
above-described techniques to take measurements from different points
of them to quantify these deformations.

Consider the [Samsung Galaxy Note 20][0] rear camera, which is 108
megapixels (presumably 9000×12000) with, if I understand correctly,
0.8 μm pixel pitch; it is capable of delivering 720p video at 960
frames per second.  (0.8 μm would give us a sensor physical size of
7.2 mm × 9.6 mm, which is quite reasonable.)  For the pixels of such a
sensor to correspond to 130-nm regions of the surface being focused
on, the optics need to magnify it by only about 6.1×, which is to say
that the focal-plane sensor would need to be 6.1× further from the
lens plane than the surface is.

[0]: https://en.wikipedia.org/wiki/Samsung_Galaxy_Note_20

You might think that this technique would require a specially printed
pattern on the glass scale, and indeed that is the standard approach,
but that is not necessary; you can simply store high-resolution
photographs of the whole scale and match the image against them.
Relatively efficient algorithms for this kind of thing are used for
precise orientation by star tracking in spacecraft, but that really
isn't important; a particle filter would probably be perfectly
adequate, since most of the time your new position and velocity can be
nearly extrapolated from the old ones.  A meter is only about 6
million pixels long at 130-nm resolution, so probably a few tens of
megabytes would suffice for a long enough map.

As I wrote in Dercuano in a note on sparkle servos, resolution can be
improved with moiré effects.  In this case, though, the "moiré
grating", which obscures part of the field of view except for some
subpixel-sized holes, needs to be in the optical near field of the
glass scale to work, say positioned within 200 nm or so of it.  But if
another transparent object is scraping against the fused-quartz scale,
not only can it deform it elastically, but also it will either scratch
the glass, be scratched itself, or both.  So I think you need either a
lubricating film, an air bearing with high-quality filtered gas, or
some other kind of arrangement to hold the two within a few dozen
enanometers without making contact.

Other positioning feedback approaches
-------------------------------------

Hard disks routinely servo back to the same [80-nm-wide track][5]
reliably within a few milliseconds; the heads float on an air bearing
tens of nanometers away from the constantly-moving disk surface.  I'm
not sure there's any practical way to reuse this amazing feat of
engineering for nanopositioning.

[5]: https://blog.stuffedcow.net/2019/09/hard-disk-geometry-microbenchmarking/

To achieve higher positioning resolution than an optical system can
provide, a scanning tunneling microscope ("STM") may be an option.
These can provide subatomic precision (on the order of 0.01 nm) but
are not normally used as positioning servos.  Also, they are sensitive
to shock and vibration.  However, they [can be homebrewed with a few
hundred hours of work and can operate in air][6].

[6]: https://www.nanotech-now.com/James-Logajan/stm.htm

Capacitive sensors routinely achieve nanometer precision, but only
over submillimeter distances.  I'm not sure if there's a way to
improve this.

Maybe a holographic approach with light has some hope of working?

Temperature compensation metamaterials
--------------------------------------

Metamaterials similar to gridiron pendulums could potentially take the
place of fused quartz and Zerodur for applications like this.

You could imagine, for example, three concentric metal tubes, the
inner and outer tube of steel and the intermediate tube of zinc, with
the inner tube being interrupted at 0 cm, 2 cm, 4 cm, and every other
even centimeter; the outer tube being interrupted at 1 cm, 3 cm, 5 cm,
and every other odd centimeter; and the zinc tube being interrupted
at every centimeter, and soldered to the interrupted steel tube at that
point.  In this way thermal expansion of the zinc shortens the tube,
while expansion of the steel lengthens it, but by a slightly smaller
factor (about 12 ppm/K against about 30–35 ppm/K for zinc.)  By
adjusting the kerf width at the interruptions, the expansion and
contraction can be perfectly balanced.

     ---# #-------# #-------# #-------# #-------# #-------#
     #==# #==# #==# #==# #==# #==# #==# #==# #==# #==# #==#
     #--+-+--# #--+-+--# #--+-+--# #--+-+--# #--+-+--# #--+
     |  | |  | |  | |  | |  | |  | |  | |  | |  | |  | |  |
     |  | |  | |  | |  | |  | |  | |  | |  | |  | |  | |  |
     #--+-+--# #--+-+--# #--+-+--# #--+-+--# #--+-+--# #--+
     #==# #==# #==# #==# #==# #==# #==# #==# #==# #==# #==#
     ---# #-------# #-------# #-------# #-------# #-------#

Consider the section from 0 cm to 2 cm with an *effective* kerf width
*k* of 1 mm.  We have a total of 38 mm of steel pipe in the "kinematic
chain": 9.5 mm on the outside, 19 mm on the inside, and another 9.5 mm
on the outside, 40 mm - 2*k*.  And we have 18 mm of zinc pipe, in two
9-mm lengths, 20 mm - 2*k*.  Suppose its TCE is 32 ppm/K.  If the
temperature increases by 100° the zinc pipe will lengthen by 0.32%, to
18.0576 mm, thus shortening this section by 57.6 μm, and the steel
pipe will lengthen by 0.12%, to 18.0456 mm, thus lengthening it by
45.6 μm.  This already gives us a much reduced thermal expansion
coefficient, but by widening the "kerfs" to a bit more than 3 mm to
diminish the zinc with respect to the steel, we can reduce it to zero.

However, that zero depends on the precision of our effective kerf
width (which extends roughly to the middle of the solder joint), on
the precision of our estimation or measurement of the thermal
coefficients involved (which vary with temperature), and on the
precision of equality of temperature of the different components.
Still, it should be straightforward to excel invar's 1.5 ppm/K
performance.

The resulting object, if properly soldered, has strength similar to a
single layer of the zinc tubing, if it were a continuous pipe, and a
lower stiffness — the compliance is roughly the sum of the compliances
of the three pipes.

There are a number of common materials with smaller [TCE][2] than
steel, but nearly all of them are brittle: soda-lime glass (9 ppm/K),
limestone (8), granite (7.9–8.4), sapphire (5.3 or 8.1, depending on
which part of that table you believe), [tungsten carbide][4] (4.9),
brick masonry (4.7–9), graphite (4–8), industrial porcelain (4),
borosilicate glass (4), wood parallel to the grain (3–5), silicon
(3–5), mica (3, presumably parallel to the grain), carborundum (2.77),
and diamond (1.1–1.3).  Common materials in the table with
substantially larger TCEs include basically all organic chemicals
(with some polymers up into the hundreds), plaster (of Paris?)
(17 ppm/K), 304 stainless (17.4), aluminum (21–24), fluorite (19.5),
magnesium and its alloys (25–27), lead (29), wood across the grain
(30), and rock salt (40.4).  Kapton is notable in this table among
organic polymers for having a TCE of only 20, though [a different
source gives 55 for "polyimide"][3].  Unfilled epoxies are claimed to
be in the 45–65 range.

[2]: https://www.engineeringtoolbox.com/linear-expansion-coefficients-d_95.html
[3]: https://omnexus.specialchem.com/polymer-properties/properties/coefficient-of-linear-thermal-expansion
[4]: https://www.carbideprobes.com/wp-content/uploads/2019/07/TungstenCarbideDataSheet.pdf

You can do this kind of trick with materials whose TCE is arbitrarily
close together, but you need more layers of pipe; that's why
traditional gridiron pendulums had seven vertical bars rather than
five, because in the Victorian age they just had brass and steel, no
aluminum or magnesium, and even zinc was comparatively exotic.  To get
by with just three layers you need materials whose TCE differs by more
than a factor of 2.

By using materials further apart in TCE, such as brick masonry and
magnesium alloys or rock salt, you may be able to get by with much
smaller "compensatory pieces".  Brick masonry has the additional
advantage that it is cheap enough and hard enough that you can achieve
very high stiffness with it at a reasonable cost.

A different metamaterial approach would be to use leverage to amplify
small differences in thermal expansion into larger compensating
contractions.