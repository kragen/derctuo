I was watching a porn video yesterday, and as the model poured mineral
oil all over her body, I was struck by the thought that the specular
reflections of the room that were appearing in the oil contained
enough information to reconstruct a fairly precise three-dimensional
model of the surface of her body, particularly given two simultaneous
images from different points of view, or if she were to rotate without
deforming.

The forward problem is relatively straightforward: you have a surface,
the surface has some Lambertian texture, some Phong exponent, and some
percentage specularity, and the surface is in some environment with
some lighting and reflecting some scene around it, in front of and
occluding part of that same scene; and the surface has some
orientation in space that is changing.  Given all these parameters,
it's straightforward, if somewhat expensive, to do the computation to
ray-trace a photorealistic image.

By solving the inverse problem through iterative methods, and in
particular methods based on the *difference between* corresponding
points on the surface at different rotations, you can *estimate* the
surface, the texture, the Phong exponent, the specularity, the scene,
the lighting, and the orientation.  Generally each part of the scene
is reflected in several places on the surface.  Most of these
parameters are of low dimensionality or effectively so; a small number
of spherical harmonics, for example, suffice to approximate Lambertian
lighting fairly precisely, and of course the lighting is itself part
of the scene.  Only the surface geometry, the texture, and the scene
are of high dimensionality, and given a few frames of video, they are
amply overdetermined.

Spilling some water on my mate and observing the sparkly reflection
around the powdered yerba, I am reminded that the Phong specular
blurriness exponent is generally taken to be an approximation of
surface microfaceting, and one of the major effects of such wetting is
to make such microfacets larger, so you can actually see them
individually.  This allows you to track them from frame to frame, even
if the surface's Lambertian texture is too uniform.

If you have two different linearly polarized cameras, you can use
Brewster's angle to additionally estimate the refractive index of the
surface gloss, and this polarization data gives you an additional
measurement of the angle and magnitude of the surface normal, as
projected on the focal plane.  This should serve to improve surface
reconstruction further.

To date, specular reflection has been a major obstacle to
photogrammetry, handled only in special cases (like flat reflecting
mirrors placed in a scene) or not at all; the standard advice is, to
accurately scan the geometry of a highly reflective object, cover it
in paper tape or cornstarch.  This approach, if it works, would turn
that advice on its head — you might find yourself wetting objects, or
pouring mineral oil on people, to get a more precise 3-D model of
them.