[Ben Krasnow, aka Applied Science, did a wonderful video][0] on
fabricating [rugate filters][1] by electro-etching heavily-doped
P-type silicon wafers (≤10mΩ·cm) at 10–100 mA/cm² in aqueous HF
(1:1 — 50% HF, I think w/w) mixed 1:1 with ethanol (50% v/v) as a
depolarizer.  The silicon superlattice thus anodized onto the surface,
layer by layer, has an index of refraction determined by the
electrical current density used to porosify it at that moment, and
consequently has a butterfly-wing-like spectrum that is the Fourier
transform of the time-domain current signal.

[0]: https://www.youtube.com/watch?v=iwj78pR46zM
[1]: https://en.wikipedia.org/wiki/Rugate_filter

This is an astonishing and unique property, and it opens the door to
fabricating not only cheap dichroic filters but also, by applying the
current in a spatially varying way (for example, by spatially
modulated UV light on a photosensitive N-type wafer during etching as
Krasnow suggests, or by any of the methods described in [the note on
foam electro-etching](foam-electro-etching.md)) the fabrication of
general graded-index optics and color holograms on the surface of the
silicon.

Graded-index optics avoid discontinuous changes of index; if the index
changes over many wavelengths rather than a fraction of a wavelength,
this entirely avoids the interfacial reflections that produce the
stray light that plagues optical systems.

Even more amazingly, Krasnow demonstrates how to separate the
microporous silicon lattice thus formed from the silicon substrate,
which is opaque to visible light, though somewhat reflective.  (I
suspect that the useful refractive-index property will disappear for
infrared light, for which the substrate is transparent, though Krasnow
claims they should work *better* at those frequencies.)  By turning
the current up high enough, the new layer of microporous silicon being
formed underneath the previous layers is so diaphanous that a simple
water wash can separate the previously-formed layers from the wafer!

One of several surprising things about this process is that HF doesn’t
normally etch Si; it’s used as a specific wet etch in semiconductor
fabrication to remove SiO₂ without attacking the silicon.  Krasnow
explains that, even without the current, silicon is not totally
invulnerable to HF, limiting the time span of this process.

Krasnow also points out that the filter material’s transmissivity to
blue light cannot reach 100%.