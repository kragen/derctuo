Can compressed sensing make a better oscilloscope?

The STM32 has a 1Msps 12-bit ADC, and [there are oscilloscope projects
using it](https://tomeko.net/miniscope_v2c/).  But a decent
oscilloscope has at least 20MHz of bandwidth, and the Miniscope has
461kHz, so it’s about 2.3% of a decent oscilloscope.

Actually though the STM32F103C8T6 used in that project and in the Blue
Pill has *two* such ADCs.  If you apply them both to the same input,
though, you won’t get any more information because (IIRC) they sample
in sync.  This is ideal if you’re trying to measure the
voltage-current characteristics of some device but suboptimal if you
want to measure a single signal faster.  You could perhaps put an
analog filter on one of the inputs in order to phase-shift some signal
components.

But what if you can fire the ADCs, or an external sample-and-hold
circuit feeding them, at effectively random times?  Then you could
sample the signal in a time-domain basis that’s incoherent with
respect to its frequency-domain content.  Then maybe you could use a
standard ℓ₁-minimizing basis-pursuit algorithm to look for a sparse
frequency-domain signal that explains what you saw?

It seems like that might be enough to get you an effective 20MHz or so
of bandwidth, though of course only for signals that really *are* sparse.
