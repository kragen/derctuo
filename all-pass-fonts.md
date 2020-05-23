You can use all-pass FIR filters to efficiently do subpixel letterform
positioning of pixel fonts as well as obviate hinting.  Pre-emphasis
filtering can mitigate the readability loss from nonzero-size pixels
and eye defocus.  This can improve [text rasterization][0].  As far as
I know, nobody is doing this, so I don’t know it will work.

[0]: https://freddie.witherden.org/pages/font-rasterisation/

Fractional-delay all-pass FIR filters for spatial translation
-------------------------------------------------------------

There are a variety of fractional-delay filters commonly used in music
for, e.g., Karplus–Strong delay lines.  The optimal filter is a
sampled sinc; with a delay of 0 or some integer number of samples,
this has an impulse response of 1 in sample 0 or some other sample and
0 on all other samples, but when its delay is some noninteger number,
all the samples are nonzero.  Sinc itself dies off annoyingly slowly,
but you can window the sinc to get a faster die-off (Lánczos
resampling being one implementation of this), and uniform basis
splines are another less explicit way to get an approximately windowed
sinc with a limited basis.  As de Boor’s “B(asic)-Spline Basics”
explains, these splines form a partition of unity, unlike the Lánczos
kernel.

The same approach can be used to translate a sampled pixel image by
some fractional number of pixels.  If the source and target have the
same resolution, this is just a convolution, with a kernel depending
on the fractional part of the shift; if the original image is bilevel
(black and white, so every pixel is either 1 or 0) doing this
convolution in the spatial domain amounts to selectively adding up
some of the weights in the convolution kernel to generate each output
pixel, those that happen to land on white pixels.  This therefore
requires no multiplications.

If the source image has resolution higher than the target by some
integer factor *n*, such as 2, 3, or 4, then I think this approach is
still mostly valid, but now instead of a single convolution kernel you
have *n*² of them, such as 4, 9, or 16 kernels, each a sampled sinc
whose frequency is at the destination resolution.  In particular, you
can use an outline letterform rasterized to a high-resolution bilevel
image to compute a grayscale image rasterized with perfect resampling
(limited only by rounding), or very good resampling (limited by
rounding and windowing).  And the high-resolution bilevel image can be
quite compact.

In particular, I think this gets rid of hinting.  Hinting is a set of
hacks which, among other things, deforms letterforms so that their
stems and curves align more often with pixel centers and their borders
run, as much as possible, halfway between pixel centers; this is
important because, without that alignment, you lose spatial
information about where they are to the sampling operation.  This
works very poorly with animation and with subpixel glyph positioning.
But sinc filtering spreads that lost spatial information out to the
surrounding pixels in the form of ringing, and as it happens, your
eyes can pick up on that.  So you shouldn’t need hinting.

Of course, on an LCD, you should sample at the LCD subpixels, usually
R, G, and B from left to right, not to the square pixels containing
them.

Efficient low-precision implementation with a multiplier
--------------------------------------------------------

This operation of convolving a bilevel image with a convolution kernel
has something of the flavor of binary long multiplication by an
element of the kernel; each bit determines whether or not to add that
weight at a particular spatial position in the output.  And indeed you
can carry it out with a multiplier under appropriate circumstances.
Take the row of pixels 0011100111100001.  Suppose 4 bits of grayscale
in the output is enough; let’s space out that number into a 64-bit
word by inserting zero bits, so it becomes 0x0011100111100001.  If we
multiply this by a 4-bit weight such as 3, it becomes
0x0033300333300003.  Suppose the next weight to the right is 4, and
the next pixel to the right is 1, so we shift in that 1 on the right
and get 0x0111001111000011, then multiply by 4 and get
0x0444004444000044, which we can add to the previous result to get
0x477304777300047, as well as the results from doing the same thing
with the corresponding weights in the next row of the convolution
kernel and the corresponding input pixels in the next (previous) row.
Proceeding in this way I think we can get perhaps an 8× to 16× speedup
over the straightforward convolution algorithm, at the expense of
really miserable overflow behavior.  The speedup is probably only 2×
or 4× against a straightforward SIMD algorithm if you have SIMD
instructions.

Because of the overflow behavior, you can’t use 2’s-complement for
negative weights, which of course are everywhere in sampled sinc
kernels.  Two possibilities occur to me: represent the weights in
sign-magnitude form, using the sign bit to determine whether to
subtract or add the product from the running sum, or use an excess-N
representation for the weights and the running sum, subtracting N
from each pixel after each multiply-add.

Low-rank approximations
-----------------------

Low-rank approximations of the relevant sinc kernels may be useful in
reducing the windowing error at a given computational load, and the
SVD provides an easy way to find them; see notes/svd-convolution.html
in Dercuano for details.

Nonzero-area pixels and pre-emphasis
------------------------------------

Above I said that sinc resampling can produce a perfectly resampled
image, but there are a couple of complications.  First, conceptually
the sampling comb is made of Dirac deltas, which concentrate a nonzero
amount of energy into a point in space.  But we live in a universe
where doing that would require creating a black hole, which is both
practically difficult and highly radioactive, so instead we
approximate it by illuminating or darkening pixels of finite, nonzero
size.

This amounts to convolving this ideal sampled signal with the shape of
a pixel, which acts as a zero-phase low-pass box filter with a sinc
frequency response.  The blurring of pixels by CRT beam dispersion or
old-person eye defocus adds an additional low-pass characteristic, but
one that’s harder to measure.  Since the pixel shape is smaller than
the pixel spacing, its first null is well above the Nyquist frequency,
so this low-pass characteristic can be corrected by “pre-emphasis”:
zero-phase linear time-invariant filtering of the original signal to
attenuate the strongest frequencies and amplify the weaker ones,
giving a perfectly flat frequency response.  You may be able to fold
this into the resampling filter described earlier, or you may want to
do four high-pass IIR-filter passes in the four cardinal directions.

One-dimensional translation
---------------------------

An important special case of subpixel text spatial translation is
horizontal translation.  I think it’s possible to use just a
fractional delay filter in the X-axis in this case, dramatically
reducing the computational cost.
