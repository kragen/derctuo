It occurred to me that if you wanted to upsample a signal by a lot, it
would be very convenient to have a sparse approximation of a sinc at
the original sampling rate, sampled at the new sampling rate ---
"sparse" in the sense that you don't need very many multiplies.

To be concrete, suppose you have a 1024-sample fragment that was
captured at 50 Msps, and you would like to upsample it to 300 Msps,
about 6144 output samples, depending on how you handle the ones at the
ends.  1024 of these output samples are simply the original 1024
samples, but the others have relatively significant contributions from
a lot of original samples: for a given output sample, about 15 input
samples have contributions attenuated by less than 20dB, about 150
have contributions attenuated by less than 40dB, and almost all have
contributions attenuated by less than 60dB.  So if you want an output
waveform whose error is 60dB or better below the signal --- and this
may well be what you want if some parts of the signal are 60dB quieter
than others --- then you will not reach your objective by doing a
time-domain convolution with a windowed sinc, unless you're willing to
do the whole 5242880-multiply-accumulate job.

Standard, well-known solutions
------------------------------

There are several standard answers to this.

One is to use Lanczos2 or Lanczos3 interpolation and call it good: the
signal you produce has very significant errors if compared to a truly
bandlimited signal, but it also has very good locality, which is
sometimes preferable.  The lanczos3 kernel has six periods of support,
so in one dimension each output sample (of those that must be
interpolated) is a weighted sum of six input samples, so you only need
30720 multiply-accumulates.

(One reason you might care a lot about locality is if you're doing
this operation "online", i.e., before you have the whole signal.  This
inherently precludes getting anything close to the right answer, since
the sinc impulse response extends very far into the past before the
impulse.)

Another is to do the Fourier transform, pad with high-frequency zeroes
to the larger size, and do the inverse Fourier transform.  (In the
more general case where the filter kernel isn't a sinc, you can do the
filtering with one complex multiply per frequency in the frequency
domain.)  I think a 1024-point FFT using the radix-2 Cooley-Tukey
algorithm requires (N/2) lg N complex multiply-accumulates, and a
complex multiply-accumulate is four real multiply-accumulates, so this
is 20 real multiply-accumulates per sample; a 6144-point FFT using the
mixed-radix version of the algorithm I think requires 22 real
multiply-accumulates per sample.  So this ends up being 155 648 (real)
multiply-accumulates, which is five times slower than the Lanczos3
approach, but unlike Lanczos3, gives the correct answer except for
rounding errors.

(Rarely noted is that it's reasonable to do the Lanczos time-domain
interpolation with fixed-point or low-precision floating point, while
the Fourier transform really needs floating point and usually double
precision.  Nowadays, with the availability of low-precision SIMD
operations in commodity hardware, this is starting to become a
tradeoff we can make in practice even without taping out our own
chips.)

The third, and I think the most used in this sort of context, is to
pad with high-frequency zeroes *in the time domain*, inserting in this
case five zero samples in between each pair of the original samples,
then low-pass filter *in the time domain* to interpolate.  Sometimes
this is more efficient if done in two or more stages, and there are a
lot of options for how to do the time-domain filtering, which can give
you arbitrarily small errors if you're willing to pay arbitrarily
large computational costs and especially if you can use acausal
filters.

The `filtfilt` function in Octave or SciPy applies a given IIR filter
twice, once going forward in time and once going backward, to cancel
out its phase shifts and incidentally double its stopband suppression
(and passband ripple).  So if we have a low-pass IIR filter in the
usual form that gives us, say, 30 dB of stopband suppression, then
using it with `filtfilt` would give us 60 dB of stopband suppression
and no phase distortion.  I don't know very much about signal
processing but I think the following interaction with Octave means
that a 16th-order elliptic filter would mostly do the job, though with
some error in the top 1% of the Nyquist frequencies (the top 250 kHz
of our 25 MHz Nyquist frequency):

    >> ellipord(.99/6, 1.01/6, .000001, 30)
    ans =  16

I think `[B, A] = ellip(16, .000001, 30, 1/6)` actually computes the
filter, but I haven't tested it and don't have enough experience to be
very confident.

So, if that's correct, then this gives us something very close to the
right answer with 32 (real) multiply-accumulates per upsampled sample
on each of the two passes, for a total of 64×6144 = 393 216
multiply-accumulates.  This is comparable to the Fourier approach, but
if we were slightly less enthusiastic about precision, we can get it
to be even more comparable.  For example, suppose our 50 Msps is
actually sampling a signal that's bandlimited to 20 MHz, so maybe we
don't really care what happens between 20 MHz and the Nyquist 25 MHz;
then we could use only a 9th-order elliptic filter:

    >> ellipord(.8/6, 1/6, 1e-6, 30)
    ans =  9

And if we additionally can tolerate passband ripple of 0.001 dB, or
0.0005 dB per pass, we can get down to a 7th-order filter:

    >> ellipord(.8/6, 1/6, .0005, 30)
    ans =  7

So now we only need 28×6144 = 172032 multiply-accumulates.

Kooky sparse solution
---------------------

So the sinc impulse response is, you know, one big double-wide hump in
the middle, and then a bunch of wiggles, all of the same width and
almost the same shape.  What if you could factor those oscillations,
or most of them, into linear combinations of a small number of basis
functions?  Like, maybe something that looks like a single hump
between two zeroes separated by the sampling interval, maybe with some
tails on it extending out into the neighboring sampling intervals.
Like a Gabor wavelet, maybe.  Or maybe just the near-sinusoidal hump.
And then maybe one or two things that represent the biggest deviations
in those wiggles, since some of them near the double-wide hump are
kind of skewed to the left or the right, although the ones way out in
the boondocks are fairly precisely sinusoidal.

You can make a sparse impulse train which, convolved with your single
hump or Gabor or whatever, and added to the doublewide hump, gives you
a pretty good approximation of the sinc.  Convolving with an
exponentially decaying alternating impulse train is easy enough (it's
a feedback comb filter: y(n) = x(n) - αy(n-k)) but in this case we
want to convolve with an impulse train that decays subexponentially.
Here are some possible solutions:

1. Try to approximate 1/n as a sum of decaying exponentials.
Unfortunately this has a sort of discontinuity or step function where
each new exponential begins.

2. Try to approximate 1/n as a sum of functions that eventually tail
off into exponential decay but have a substantial subexponential
plateau before that.  For example, you can have a pipeline where your
input signal goes into one stage of exponential ringdown, which is
used to excite an output stage that does another exponential ringdown,
perhaps a much slower one.  The impulse response of this system should
be a gradually growing alternating impulse train which asymptotes up
to some maximum amplitude as by exponential decay, then dies away with
essentially the exponential-decay behavior of the output stage.  This
two-stage pipeline avoids having a step function, but it still has a
step-function discontinuity in the *derivative* of its envelope.

3. A three-stage pipeline can have an envelope that looks like the
integral of the envelope of the two-stage pipeline, with a smoothly
feathered sigmoid startup, followed by the usual exponential decay
(which stabilizes the otherwise inherently unstable integrator).  This
pushes the discontinuity in the envelope out to the second derivative.

3. Use one or two stages of feedforward combs to sort of flatten out
and shape the plateau of a two- or three-stage pipeline.

4. Way out in the far tails, use different Gabor basis functions that
have many oscillations in their envelopes, not just a few; that way
you can use impulse trains of much lower frequencies.  I'm not sure
this actually helps because the cost of convolving with an alternating
impulse train using a feedback comb filter isn't dependent on the
frequency of the impulse train.

5. The Gabor basis function in particular has an existing efficient
sparse approximation that I've written about previously in Dercuano.

I don't know enough yet to know whether this will yield a more
efficient and/or precise solution than the standard time-domain IIR
bidirectional filtering approach, but it does seem plausible.  In
particular a great deal of the signal processing in this approach can
be done at the lower frequency rather than the higher frequency.