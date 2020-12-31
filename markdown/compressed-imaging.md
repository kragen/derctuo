In compressed sensing we sense a signal, for example an image, via
some kind of linear basis that’s incoherent with respect to some
underlying basis in which we expect the signal to be sparse, and then
attempt to estimate a sparse signal in that underlying basis that best
"explains" our observation.  If we are correct in our prior that the
signal should be sparse in that underlying basis, this does a great
job at reproducing the true signal.  (And often we can choose the
underlying basis such that when we're wrong about that, it’s one of
the cases we care less about.)

It occurs to me that you can use this for producing images as well.
Consider, for example, a disco-ball sparkle pattern swept over a wall
while being illuminated by a rapidly modulated LED (or three).  A
camera or eye will sum many successive positions of the sparkle
pattern together due to the persistence of vision, and the brightness
and color of these positions will depend on the brightness of the LED
at that moment.  These may be sufficiently incoherent with respect to
a suitable basis such as the Fourier basis as to be able to sum to an
arbitrary visually coherent image.

They may not, though, and the inability of the LED to emit negative
light may be a serious limitation here, since it limits the image’s
dynamic range, potentially rather badly (like to 3:1 or 4:1 rather
than the 100:1 of a good LCD or CRT.)  Other candidate output devices
for such compressed imaging include:

- A rotating sparkling surface illuminated by a time-domain-modulated
  light, or several, viewed from a single point.
- A piece of sandpaper with grains in random but known positions,
  rotated over a surface while floating on a cushion of air, then
  whacked into the surface at precise moments by a hammer at one
  location or another.
- A sparkle pattern produced by refraction or reflection through two
  or more random but known optical surfaces, either fixed or in known
  motion with respect to one another.
- A collection of multi-pointed electrodes swept over a surface with a
  time-domain-modulated electrical current on each one to deposit
  and/or remove and/or functionalize material, for example through
  electroplating and electro-etching, through plasma surface
  activation, or through vaporizing parts of the surface.

If you use an optimization algorithm in a Fourier-like basis whose
objective function selectively neglects phase and precise frequency,
you may gain useful degrees of freedom with respect to human vision
and audition, among other things: the humans can't hear the phase of
the tenth harmonic of a vocal signal, nor see if all the hairs in an
area of a closeup photo of a person have been shifted half a
hairsbreadth to the right, nor hear the difference between 60Hz and
60.1Hz.  This optimization approach is of course also useful for
applications like mural design, JPEG compression, and adapting sound
reproduction to the resonances of a given listening space.
