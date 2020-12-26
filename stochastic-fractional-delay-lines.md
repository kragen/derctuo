One of the key primitives for constructing delay-line synthesis sounds
like Karplus–Strong is the fractional-delay filter, but computing this
filter is often computationally more expensive than the rest of the
delay line, even if [it’s a first-order FIR filter][0].  I think a
simple stochastic version of this filter is likely to be good enough
for many applications and dramatically cheaper to compute.

[0]: https://ccrma.stanford.edu/~jos/Interpolation/Linearly_Interpolated_Delay_Line.html

If, for example, the resonance you want is a lag of 22.32 samples (at
CD-DA’s 44.1 ksps, that’s 1976 Hz, B₆, more than an octave below the
top of a piano) then a lag of 22 samples would give you 2004 Hz, about
24 cents sharp — a very conspicuous tone difference to the ear — and a
lag of 23 samples would be even worse.  Worse, if you’re changing the
delay over time (for example, for a vibrato), the sudden change in
pitch would be even more conspicuous.  So you *really need* a
fractional-sample delay.  [As Julius Smith explains][1], the easiest
way to do this is to calculate the lerp (1 - *η*) *y*(*n*) + *ηy*(*n*
- 1) = *y*(*n*) + *η*(*y*(*n* - 1) - *y*(*n*)), where *η* is the
desired fractional delay, 0.32 in this case; this requires one
multiply per sample.  (There’s also a [feedforward first-order allpass
alternative][2] with the same computational cost, less phase
distortion, no low-pass filtering loss, but less ability to handle
variable delays: *y*(*n*) = *η*(*x*(*n*) - *y*(*n* - 1)) + *x*(*n* -
1), with the delay being roughly (1 - *η*)/(1 + *η*).)

[1]: https://ccrma.stanford.edu/~jos/pasp/Linear_Interpolation.html
[2]: https://ccrma.stanford.edu/~jos/pasp/First_Order_Allpass_Interpolation.html

But a multiply is much more costly than an add.  A different way to
achieve the same effect is to *randomly* choose a lag of 22 or 23
samples *on every sample*, with probabilities 0.68 and 0.32.  This
will give the desired *average* lag of 22.32 samples, but it can be
done much more efficiently than a multiply: an 8-bit LFSR and an 8-bit
comparator, for example, would suffice, and these take much less
circuitry than even an 8×8-bit multiplier, much less an 8×16
multiplier or 8×24.

The resulting phase noise will tend to introduce white noise modulated
by high-frequency components and time-domain transients of the signal.
This can be somewhat diminished by switching between the lags less
often than every sample, perhaps every 4–8 samples.  And if the
cheaper fractional-delay filter allows you to use a higher sampling
rate, that may have a larger effect than this noise.

A different approach to mitigating the white-noise modulation problem
is to synthetically add white noise where it wouldn’t otherwise be
necessary, so that this technique adds even more noise, but
consistently rather than at a rate modulated by the signal’s maximum
slew rate and the momentary value of the lag.

This stochastic-fractional-delay technique can be applied to a variety
of other applications: [Paeth (and Minsky-circle) rotation of raster
images](knuth-paeth-minsky-rotation.md), adding vibrato or flanging to
an existing audio signal, fractional-delay resonators for tone
recognition (including radio-frequency tone recognition), calculating
optimal sampling times for clock and data recovery in asynchronous
communication, and phased-array beamforming.

Paeth rotation
--------------

In the standard version of Paeth three-shear rotation, shearing is
done by shifting rows and columns of pixels by integer numbers of
pixels, which approximate fractional-pixel shifts.  But the rounding
of the shifts produces aliasing artifacts, which can be diminished by
fractional-delay filtering.  Different tradeoffs are possible here.  A
single row or column can be shifted by a fractional amount by sampling
individual pixels, or runs of pixels, at randomly different “lags” or
shift distances, which will tend to fuzz out the rows or columns that
have ideal shifts further from any integer, like 33.5 pixels rather
than 33.9 or 33.1, similar to how just doing bilinear filtering on
them (the lerp fractional-delay approach described above for audio)
would fuzz them out, but also adding noise.  As before, randomly
selecting lags for *runs* of samples (pixels) rather than individual
samples will tend to reduce this noise.

(Shearing using an all-pass filter, again as described above for
delay-line music synthesis, would eliminate the shift-dependent
fuzzing-out low-pass filtering, still costing one multiply per sample.
Also, JOS claims the all-pass approach isn’t suitable for “random
access” — computing output sample *n* without computing all the *n*
output samples before it, which is obviously relevant to tiled
rendering of images — because it’s recursive; but presumably you can
apply the standard prefix-sum algorithm to the recurrence relation to
get a more efficient way to do random access.)

Sampling different pixels in the same row (or column) being shifted
will result in making zero copies of some of them and two copies of
others, thus degrading the image somewhat.  A different approach is to
generate the random lags on a per-row (or per-column) basis, so all
pixels in a row (or column) are shifted horizontally (respectively,
vertically) by the same amount, and no pixels are lost or duplicated.
This eliminates much of the information loss and also takes the extra
work out of the inner loop.

A further step to reduce the information loss and extra work is to
make these rounded lags of successive rows (or columns) monotonic: if
there’s a succession of rows to be shifted by 33.0 pixels, 33.2
pixels, 33.4, 33.6, 33.8, and 34.0, then we pick a random breakpoint
among the fractional shifts where we switch from rounding to 33 to
rounding to 34.  This should still provide enough randomness to break
up the most objectionable aliasing patterns.

Vibrato and flanging
--------------------

Vibrato on a musical instrument alters its pitch slightly in a
periodic manner; in most cases a good approximation can be achieved by
slightly advancing and retarding the signal, although of course if
taken to an extreme this would start swinging the tempo too.  Flanging
is pretty much by definition just a matter of retarding one copy of
the signal by a variable amount.  So these techniques are directly
applicable.

Resonators for tone recognition
-------------------------------

A piano string is a delay-line resonator that can recognize particular
audio frequencies (or their harmonics).  The same technique can be
applied with a Karplus–Strong delay-line resonator in a relatively
small amount of memory; also, though, if we have enough memory to
store the whole input signal, we can quite reasonably convolve it with
a sparse impulse train in order to detect periodic motifs in it at the
relevant frequency, whether sinusoidal or of some other form, which
allows us to choose a different shape for our temporal window than the
exponential rise and decay the constant-space cyclic delay line would
seem to limit us to.  The various fractional-delay techniques
described above can allow us to effectively position these impulses at
fractional-sample positions.

This, of course, also works for variable-frequency and radio-frequency
tones, for example for FM radio reception.

Clock and data recovery
-----------------------

The simplest form of clock and data recovery is just a question of
extracting the phase of a periodic motif of a known frequency and
shape from noisy data, and of course there’s no guarantee that the
period of that frequency has an integer number of samples.

Phased-array beamforming
------------------------

In phased-array beamforming we sum up the signals received at many
different transducers from a given source (or do the equivalent with
time and causality reversed).  Depending on the direction and
sometimes the distance to the source, the delays from the source to
these different transducers are different, and this permits us too
separate the source’s signal from other signals.

If everything is known except the signal, this is just a matter of
applying the right lags to the different signals and adding them up.
A fast way of applying different fractional-sample lags to different
signals is thus useful.

(But it’s hard for me to imagine that it can compete with an FX
correlator on precision, so you'd probably only do it when the Fourier
technique is too expensive.)
