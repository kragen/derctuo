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
distortion, but less ability to handle variable delays.)

[1]: https://ccrma.stanford.edu/~jos/pasp/Linear_Interpolation.html
[2]: https://ccrma.stanford.edu/~jos/Interpolation/First_Order_Allpass_Interpolation.html

But a multiply is much more costly than an add.  A different way to
achieve the same effect is to *randomly* choose a lag of 22 or 23
samples *on every sample*, with probabilities 0.68 and 0.32.  This
will give the desired *average* lag of 22.32 samples, but it can be
done much more efficiently than a multiply: an 8-bit LFSR and an 8-bit
comparator, for example, would suffice, and these take much less
circuitry than even an 8×8-bit multiplier, much less an 8×16
multiplier or 8×24.

The resulting phase noise will tend to introduce white noise modulated
by high-frequency components of the signal.  This can be somewhat
diminished by switching between the lags less often than every sample,
perhaps every 4–8 samples.  And if the cheaper fractional-delay filter
allows you to use a higher sampling rate, that may have a larger
effect than this noise.

This stochastic-fractional-delay technique can be applied to a variety
of other applications: [Paeth (and Minsky-circle) rotation of raster
images](knuth-paeth-minsky-rotation.md), adding vibrato or flanging to
an existing audio signal, fractional-delay resonators for tone
recognition (including radio-frequency tone recognition), calculating
optimal sampling times for clock and data recovery in asynchronous
communication, and producing direct-digital synthesis
frequency-modulated signals for software-defined radio.
