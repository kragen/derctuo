Methods for two-dimensional rotation with two or three real multiplies
======================================================================

Method K for complex multiplication
------------------------------------

Wikipedia says Knuth gives an algorithm for multiplying (*a* +
*bi*)(*c* + *di*) as follows: (*k*₁ - *k*₂) + (*k*₁ + *k*₃)*i* where
*k*₁ = *a*(*c* + *d*), *k*₂ = (*a* + *b*)*d*, *k*₃ = (*b* - *a*)*c*,
which works out to (*ac* - *bd*) + (*ad* + *bc*)*i* as it should.
Call this Method K.  Method K is interesting because often
multiplication is much more expensive than addition or subtraction
(for example, it takes much more space in hardware in fixed point, and
much more time in multiple precision), and this algorithm requires
only three real multiplies, rather than the four required by the more
direct approach.

In Unicode matrix form:

    ┎             ┒┎  ┒     ┎  ┒
    ┃  1  1  0  0 ┃┃ac┃     ┃k₁┃
    ┃  0  1  0  1 ┃┃ad┃  =  ┃k₂┃
    ┃ -1  0  1  0 ┃┃bc┃     ┃k₃┃
    ┖             ┚┃bd┃     ┖  ┚
                   ┖  ┚

    ┎           ┒┎  ┒     ┎ ┒
    ┃  1  -1  0 ┃┃k₁┃     ┃ℜ┃
    ┃  1   0  1 ┃┃k₂┃  =  ┃ℑ┃
    ┖           ┚┃k₃┃     ┖ ┚
                 ┖  ┚

Because

    ┎           ┒┎             ┒┎  ┒
    ┃  1  -1  0 ┃┃  1  1  0  0 ┃┃ac┃
    ┃  1   0  1 ┃┃  0  1  0  1 ┃┃ad┃ =
    ┖           ┚┃ -1  0  1  0 ┃┃bc┃
                 ┖             ┚┃bd┃
                                ┖  ┚
                   ┎  ┒
    ┎             ┒┃ac┃
    ┃  1  0  0 -1 ┃┃ad┃
    ┃  0  1  1  0 ┃┃bc┃
    ┖             ┚┃bd┃
                   ┖  ┚

Karatsuba multiplication
------------------------

A very similar insight underlies Karatsuba multiplication for
multiple-precision real numbers; I don't know if Karatsuba was working
directly from the above complex algorithm, but in Karatsuba
multiplication we obtain (*a* + *br*)(*c* + *dr*) = *ac* + (*ad* +
*bc*)*r* + *bdr*² by calculating *j*₀ + (*j*₂ - *j*₁ - *j*₀)*r* +
*j*₁*r*², where *j*₀ is of course *ac* and *j*₁ is of course *bd*,
from which we can calculate that *j*₂ must be *ac* + *ad* + *bc* +
*bd* = (*a* + *b*)(*c* + *d*) — our third multiply!  (And two adds.)

So, for example, with *r* = 10, we can calculate 97 × 86 as *j*₀ = 7×6
= 42, *j*₁ = 9×8 = 72, *j*₂ = (9+7)×(8+6) - *j*₁ - *j*₀ = 16×14 - 42 -
72 = 224 - 42 - 72 = 110, so our answer is 42 + 110×10 + 72×100 =
8342, which is correct.  And recursively subdividing the problem this
way gives us Karatsuba's asymptotically faster multiplication
algorithm, the first one discovered in millennia.

Applied to the special case *r* = *i*, Karatsuba multiplication gives
us *j*₀ - *j*₂ + (*j*₂ - *j*₁ - *j*₀)*i*, so Karatsuba multiplication
gives us a slightly different three-real-multiply algorithm for
complex multiplication.  It uses two adds and three subtracts in
addition to the multiplies, while Method K uses three adds and two
subtracts — virtually the same computational cost.

(Incidentally, the version of Method K given in Wikipedia interchanges
*k*₂ and *k*₃, as well as *a* and *c*, and *b* and *d*.  That's
because I reconstructed it from memory and it has an asymmetry with
respect to its arguments that Karatsuba multiplication does not.)

Partial evaluation
------------------

If we partially evaluate these two algorithms with respect to one of
the arguments, say we hold constant the (*a*, *b*) argument, Method K
eliminates an add and a subtract, because *k*₂ = *m*₀*d* and *k*₃ =
*m*₁*c*, where the *mᵢ* depend only on the constant argument.
Partially evaluating Karatsuba multiplication in the same way only
eliminates the *a* + *b* add.  So for complex multiplication by a
constant multiplier, Karatsuba costs three multiplies, one add, and
three subtracts; Method K costs three multiplies, two adds, and one
subtract; and the basic method costs four multiplies, one add, and one
subtract, just as for non-constant arguments.

Complex multiplication for rotation and scaling
-----------------------------------------------

In the context of computer graphics, complex multiplies are
particularly interesting because they perform uniform scaling and
rotation in a single operation.  So, for example, you can take a
vector figure represented as a list of (*x*, *y*) points, and scale it
by *n* and rotate it by *θ* by multiplying each number (*x* + *yi*) by
a complex constant (*n* cos *θ* + *ni* sin *θ*).  (Typically you also
want translation, which is complex addition if you're still thinking
in complex numbers, but there's no advantage in doing so.)  If you
have the *x* and *y* components of various different points in some
SIMD registers, this costs you three SIMD multiplies, two SIMD adds,
and one SIMD subtract using the partially-evaluated Method K.

Commonly in computer graphics we instead rotate raster images; this
can be done by brute force by translating and rotating the screen
coordinates of every screen pixel into texture space by the methods
above, but strength-reducing this operation is very advantageous and
universally done.  Instead of transforming the coordinates of each
pixel, we transform the coordinates of a start pixel, the delta (1, 0)
to move one pixel to the right, and the delta (0, 1) to move one scan
line down.  These give us increments we can use to walk around texture
space with simple adds.  Then we can sample from texture space with a
variety of methods — for procedural textures like fragment shaders, we
just invoke the procedure with the transformed (x, y) arguments, while
for materialized textures we commonly use nearest-neighbor or bilinear
sampling, though there are a variety of common tradeoffs between
aliasing and computation time.

Paeth's three-shear rotation algorithm
--------------------------------------

There's another famous algorithm for rotating raster images with three
multiplies, though, by Paeth, usually called the "three-shear
rotation".  It doesn't do any scaling, but its compensating virtue is
that, in its usual form, it doesn't lose any pixels — under
appropriate circumstances it's perfectly reversible, because you
execute it by *shearing* the raster pixels by displacing them some
integer number of pixels.  This also means that it doesn't require
per-pixel sampling operations, even rounding.

The disadvantage of Paeth's three-shear rotation is that it produces a
lot of aliasing artifacts because of the constraint of shifting the
pixels only by integer amounts.

Paeth's algorithm for rotating a vector (*x*, *y*) consist of the
following three steps:

    x += αy;
    y -= βx;
    x += αy;

(And the shear transformations work by shifting pixels *αy* pixels to
the right in the first step, *βx* pixels up in the second step, and
*αy* pixels to the right again in the third step.  Normally you round
these shifts to integers.)

We can represent this calculation with matrix concatenation as follows
(here I'm copying my memory of Tobin Fricke's page on the subject):

    ┎       ┒┎       ┒┎       ┒┎   ┒
    ┃  1  α ┃┃  1  0 ┃┃  1  α ┃┃ x ┃
    ┃  0  1 ┃┃ -β  1 ┃┃  0  1 ┃┃ y ┃
    ┖       ┚┖       ┚┖       ┚┖   ┚

Let's concatenate out the matrices; first the rightmost ones:

    ┎       ┒┎       ┒   ┎          ┒
    ┃  1  0 ┃┃  1  α ┃ = ┃  1   α   ┃
    ┃ -β  1 ┃┃  0  1 ┃   ┃ -β  1-αβ ┃
    ┖       ┚┖       ┚   ┖          ┚

That is, when we subtract off *β* of *x* from *y* in the second step,
the *x* we're subtracting already has an *α* of the original *y* in
it, so the *y*-to-*y* item isn't 1.  Now the third step:

    ┎       ┒┎          ┒   ┎                   ┒
    ┃  1  α ┃┃  1   α   ┃ = ┃ 1 - αβ   2α - α²β ┃
    ┃  0  1 ┃┃ -β  1-αβ ┃   ┃   -β      1 - αβ  ┃
    ┖       ┚┖          ┚   ┖                   ┚

So now we end up subtracting off a proportion *αβ* of the original *x*
as well as the original *y*, and adjusting each one by different
fractions (respectively -*β* and 2*α* - *α*²*β*) of the other,
fractions which approach 0 if *α* and *β* do.

So, to get a matrix for rotation by *θ* out of this, we need cos *θ* =
1 - *αβ* and sin *θ* = -*β* = *α*²*β* - 2*α*, which is three equations
in two unknowns.  We have directly that *β* = -sin *θ*, and to
calculate *α* we can observe that cos *θ* = √(1 - sin² *θ*) = √(1 -
*β*²) = 1 - *αβ*.  Thus *α* = (1 - √(1 - *β*²))/*β*.

But our problem was overdetermined, so we still have to check if this
gives us -*β* = *α*²*β* - 2*α*, so we want to see if *β* = 2*α* -
*α*²*β*, which becomes  
2(1 - √(1 - *β*²))/*β* - *β*(1 - √(1 - *β*²))²/*β*² =  
2/*β* - 2√(1 - *β*²)/*β* - (1 - √(1 - *β*²))²/*β* =  
(2 - 2√(1 - *β*²) - (1 - √(1 - *β*²))²)/*β* =  
(2 - 2√(1 - *β*²) - 1 + 2√(1 - *β*²) - (1 - *β*²))/*β* =  
(2 - 1 - 1 + *β*²))/*β* =  
*β*.

So choosing *α* and *β* in this way does give us a pure rotation.

### Stupid bumbling, fix ###

 and we hope we
can find a solution for both -*β* = *α*²*β* - 2*α* and cos *θ* = 1 -
*αβ* = 1 + *α* sin *θ*.  The first gives us *β* = 2*α* - *α*²*β*, *β*
+ *α*²*β* - 2*α* = 0 = *βα*² - 2*α* + *β*.  If I haven't swapped any
signs, the quadratic formula gives us *α* = (2 ± √(4 - 4*β*²))/2*β*;
for example for *β* = ½ (a 30° rotation) we get *α* = 2 ± √(4 - 2) = 2
± √2.  This is wrong because then for either value 2*α* - *α*²*β*
gives us 1, not 0.5 as it should, although at least it's the same
value.  Also, though, acos(1 - *αβ*) in that case (either one) gives
us ¾*π*, whose sin is √2, not ½.  So it's at least consistently wrong
by a factor of √2.

Let's come at it the other way.  If cos *θ* = 1 - *αβ*, and β = -sin
*θ* = ½, *θ* = -30°, so its cos is ½√3, so *αβ* = 1 - ½√3, and *α* is
twice that, or 2 - √3, about 0.268.  Now 2*α* - *α*²*β* = 2(2 - √3) -
½(2 - √3)² = 4 - 2√3 - ½(4 - 4√3 + 3) = 4 - 2√3 - 2 + 2√3 - 1½ = ½.
So *α* = 2 - √3, *β* = ½ does give us a pure rotation, and it looks
like it really is 30°.  Maybe positive, though.

Following this back through, *α* = (1 - cos (sin⁻¹ *β*))/*β*, but this
is an unnecessarily terrible way to calculate *α*.  cos² *Θ* + sin²
*θ* = 1, so we can rewrite this as *α* = (1 - √(1 - *β*²))/*β*.  Let's
check the thing that's supposed to be identically equal to *β*: 2*α* -
*α*²*β*, which becomes 2(1 - √(1 - *β*²))/*β* - *β*(1 - √(1 -
*β*²))²/*β*² =  
2/*β* - 2√(1 - *β*²)/*β* - (1 - √(1 - *β*²))²/*β* =  
(2 - 2√(1 - *β*²) - (1 - √(1 - *β*²))²)/*β* =  
(2 - 2√(1 - *β*²) - 1 + 2√(1 - *β*²) - (1 - *β*²))/*β* =  
(2 - 1 - 1 + *β*²))/*β* =  
*β*.

Minsky's circle algorithm and two-shear image rotation
------------------------------------------------------

Paeth's algorithm is strikingly similar to the Minsky circle algorithm
described in HAKMEM, which computes an approximate rotation of a point
around the origin as follows:

    x += αy;
    y -= αx;
    # no further steps

This is, surprisingly, stable with exact math (the determinant of the
resulting matrix [1, *α*; *α*, 1-*α*²] is exactly 1) and usually even
with approximate math, including integer math.  With integer math,
even if *α* is prescaled to be something like 3/32, each of the steps
is computationally reversible, like Paeth's rotations; so orbits can't
converge, and they can't grow without bound because the determinant is
1, so they must return to the starting point.  But the circles
described by successive iterations are elliptical, which is obvious if
you start with, for example, (*x*, *y*, *α*) = (1, 1, 1) — the orbit
is (1, 1), (2, -1), (2, -1), (1, -2), (-1, -1), (-2, 1), (-1, 2), and
then repeats.  For smaller values of *α* the ellipticity is quite
small.

The reversibility property means that if you use this transformation
to map a bunch of unique pixel coordinates, the resulting pixel
coordinates will still be unique!  In fact we can implement this
"rotation" on a raster image with two Paeth-like shears, and each of
the pixels will describe a Minsky pseudocircle that never collides
with any other pixel, and eventually returns to its starting point.
The image will be distorted as it rotates (in particular any pixels
close enough to the origin that *αx* and *αy* round to zero will stay
put instead of rotating!) but it will eventually return to its
original form.  Not having tried it, I suspect that for many parameter
values, particularly with the center of rotation well outside the
image, the distortions will be imperceptibly small compared to the
aliasing of Paeth's algorithm implemented with integer shears.

Both Minsky's and Paeth's algorithm can be thought of as two timesteps
of leapfrog integration of a simple harmonic oscillator (*ẍ* = -*kx*);
the difference is that Paeth's algorithm starts halfway in between two
timesteps.  But it seems like this would imply that you can undo the
ellipticity of Minsky's algorithm by picking a different starting
position, and in fact you can't.

Paeth locality and run-length slicing
-------------------------------------

Many people have questioned whether Paeth rotation is still fast on
modern machines, quite apart from the aliasing artifacts induced by
its standard implementation with integer pixel shifts, because the
standard approach uses three passes over the image, thus blowing up
your dcache unnecessarily.  I think you can probably get enough
locality by pipelining and maybe tiling.  Suppose you break the image
into tiles of 16×16 pixels (768 bytes in fancy shmancy 24-bit color)
and your α and β factors are small enough that the shift over the
whole image is less than ±16 pixels.  Then producing a single output
tile involves only pixels from two horizontally adjacent second-stage
tiles; each of these involves only pixels from two vertically adjacent
first-stage tiles, four in all; and each of these involves only pixels
from two horizontally adjacent input tiles, six in all, spread across
two rows of tiles.

So if you pipeline the shears you only need enough dcache to hold 32
scan lines of the image, which I think is even true without tiling; if
it's the traditional 1024 pixels wide then that's 96 kilobytes in
24-bit color, which is coincidentally exactly the size of my L1D cache
on my Pentium N3700 laptop.

We can do better than this with Z-curve or Hilbert-curve ordering over
the tiles.

However, for smallish rotations, we can do *much* better; each scan
line of the output is made out of concatenated segments from different
scan lines of the input.

Consider an input image all white with one horizontal black line.  The
initial x-shear just moves the black line with the image.  Then the
y-shear breaks the black line up into something like a Bresenham line;
if the y-shear is 16 pixels over a width of 500, for example, it
consists of alternating 31-pixel (75%) and 32-pixel (25%) segments,
placed on successive scan lines; this stretches the
originally-500-pixel line into a line of roughly 500.26 pixels in
length.  Then the final x-shear may overlap some of these segments,
putting two black pixels above one another, or it may not, depending
on where the line is positioned vertically.

I think these overlaps are points where, when moving from writing into
output scan line *m* by copying from input scan line *n* to copying
from input scan line *n*±1, we also adjust the *x*-offset from which
we are copying.

Larger rotations produce shorter segments which overlap more often,
but until you get to a rotation of more than about 20°, the segments
are still substantial.  You can copy these segments directly from the
input image into their place in the output image, rather like
run-length-slice line drawing, but sometimes with overlap between the
ends of the slices, and copying pixels rather than filling colors.
