Slide rules can’t add and subtract.  Could they?

If *a* ≠ 0, then *a* + *b* = (1 + *b*/*a*)*a*.  Suppose our slide rule
reads with a precision of ±0.2%; then if *b*/*a* < 0.002, we can just
round this to *a*, and if *b*/*a* > 500, we can just round it to *b*.
But in between, when they’re of roughly similar magnitudes, we might
want to use this to calculate a decent approximation of the sum.

In Logarithm Land, we have:

> log(*a* + *b*) = log(1 + 10<sup>log *b* - log *a*</sup>) + log(*a*), *a* ≠ 0

We can add the ability to evaluate this to a slide rule as follows.
Define *j*(*c*) = log(1 + 10<sup>*c*</sup>).  Add three *j* scales to
the body of the slide rule: one with marks at log(1 +
10<sup>*c*</sup>) for 1 ≤ *c* ≤ 10, one with marks for 10 ≤ *c* ≤ 100,
and one with marks for 100 ≤ *c* ≤ 1000.

To add two numbers of the same sign, then, take the larger one as *b*.
Use the C and D scales to compute log *b* - log *a* as a position on
the D scale; move the cursor to it.  Look on the appropriate *j* scale
to read the numerical value of (1 + *b*/*a*).  Now use the C and D
scales to multiply that numerical value by *a*, which perhaps you
still have encoded in the position of the slide.

Working backwards from there, we want the slide to be in a position
that encodes multiplying by *a*, which means that the D-scale index
should be aligned with *a* on the C scale.  This means that *b* needed
to be on C originally.

So the procedure is: choose *b* to be the larger of the two summands,
exchanging them if necessary.  Align the slide to *a* on the C scale.
Move the cursor to *b* on the C scale; its position on the body is now
log *b* - log *a*, so the cursor on the D scale now indicates *b*/*a*.
Look up the numerical value of 1 - *b*/*a* on the appropriate *j*
scale with the cursor.  Move the cursor to that numerical value on the
D scale.  Now read *a* + *b* on the C scale with the cursor.

I’m not sure if there’s a similarly convenient approach using the CI
and DI scales, or if there’s a way to use that same *j* scale for
subtraction, or if it works better to use *b* < *a* (could that give
you fewer *j* scales?).

[Peter Alfeld reports that Jeff Weiner
reports](https://www.math.utah.edu/~alfeld/sliderules/) that the
Pickett Microline 115 and the Pickett 901 rules *can* add and
subtract, but it turns out that those just have linearly-ruled X and Y
scales; they can’t add and subtract numbers found on the standard
scales like C, D, A, and B, nor do their sums and differences appear
there.

Now, of course this is not very useful if your precision is ±0.2%: you
only have three sig figs, and adding two three-digit numbers in your
head isn’t that hard.  You could imagine a higher-precision slide rule
using verniers, finer details, and/or larger dimensions, perhaps
folded helically.  This approach might then be more useful.