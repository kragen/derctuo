Darius Bacon linked me to
<https://twitter.com/paniq/status/1317063053339406336>:

@paniq@mastodon.social / L. 🧩 . Ritter / @paniq:

> apparently these good people solved the explosion in steps when the
> ray gets close to the surface during SDF sphere marching (thanks to
> @Atrix256 ) <https://diglib.eg.org/handle/10.1111/cgf13951>

> i must say i'm a little angry with myself because i spent hundreds
> of hours on getting around this exact problem and couldn't find a
> better way.

> there's only two shadertoys yet <https://shadertoy.com/view/WdKczW>

> the classical sphere marching method: lipschitz continuity
> guarantees that the blue line never intersects the diagonal of the
> red stepping function, which always moves as many units forward as
> it has measured upwards.

> plotting the first derivative of our curve (green), the light green
> lines designate the global lipschitz limit that the function is
> guaranteed never to overstep.

> the purple lines bracket the local upper and lower bounds of the
> first derivative within a local region.

> plotting the limits as as a wedge of tangents, all positive, we see
> that the region can't possibly contain a root, and thus deem it safe
> to skip the entire interval.

> whereas in this less beneficial interval, our tangent bundle can
> only guarantee us safe passage up until the cyan colored point.

> this region here however allows us to make a step twice as a large
> as a simple SDF lookup would permit us.

> it is easy to see how this method becomes particularly useful when
> we are grazing surfaces with low curvature, as the tangent width
> narrows, and we can practically perform an interation of the
> newton-raphson method.

> the authors don't do a good job of showing these connections. the
> provided formula is effectively a version of the newton-raphson
> method which uses a gradient interval instead of a local gradient.

> to compute the gradient interval, you use a combination of automatic
> differentiation and interval arithmetic on the first derivative.
> here are primitives for both:
> 
> * Interval Arithmetic <https://shadertoy.com/view/lssSWH>
> * Derivative Arithmetic <https://shadertoy.com/view/4dVGzw>

> the new arithmetic primitives will likely look a lot like joint
> ranges from revised affine arithmetic
> (<https://shadertoy.com/view/4sV3zm>), except that we get a right
> facing interval cone instead.

> a much more important consequence of all this is that the lipschitz
> continuity requirement |f'(x)| <= 1 no longer applies as we always
> see a local gradient cone. you can use this to trace any old
> implicit function, not just only distance functions.

> bonus: with 1 more sample at the right end of the interval, we can
> reuse the same gradient interval, flip it, and use it to truncate
> our extrapolated destination. in this case, we discover that we can
> skip the entire interval rather than having to jump to the cyan
> point.

> the right hand samples amortize over time, as we can reuse them in
> the next iteration.

Matt Keeter @impraxical:

> Dumb question: if you can do interval arithmetic on your function,
> why not directly check the interval result in the target segment (to
> see if it contains 0), rather than the interval of the gradient in
> the segment?

@paniq:

> what do you do when the interval contains a possible root?

> Ah, that explains why this is useful :D
>
> I agree that affine arithmetic could tell you how far you could
> safely walk; I'm curious to see how "point + gradient range" (this
> paper) compares to "affine region" (RAA), in terms of ease of
> computation / tightness of bounds / etc.

@paniq:

> RAA is costly, and has overshoots i.e. false positives, which means
> you're frequently backtracking. building intervals on the first
> derivative should in many cases be cheaper.

