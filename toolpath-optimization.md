A general recipe for planning how to make things to fill some
requirement Q: given a simulation of the construction process S and a
sort of distance measurement M, minimize M(S(P), Q) for some given Q;
P is some sort of plan, such as a toolpath.

In many cases it's convenient to separate the simulation S(P) into a
simulation C of the construction process and a simulation E of the
usage or testing of the artifact produced by it, E(C(P)).  For
example, C might convert a toolpath into 3-dimensional geometry and an
expected cycle time, and E might perform a finite element analysis and
preserve the cycle time.  In this case our loss function expands to
M(E(C(P)), Q).

This simple recipe ramifies in all sorts of interesting ways.

Probably nothing in here is original; it's all pretty obvious to
someone who knows this stuff.  But I haven't seen it presented this
way anywhere else, it wasn't obvious to me, and I don't see it used in
practice much.

Construction simulation C
-------------------------

Most CAM systems have very simple simulation capabilities, sometimes
nothing more than incrementally setting voxels to zero or even, for a
CNC lathe, pixels.  Consequently the most thoroughly automated
manufacturing processes have been those like single-point metal
cutting in which the interaction between the tool and the workpiece is
very simple.  Modern waterjet cutting systems are learning to
compensate for waterjet divergence.  But the modern simulation
techniques used in systems like ANSYS are capable of simulating
extremely complex systems, and simulation of, for example, work
hardening makes it possible to simulate the dynamics of sheet metal
stamping or single-point incremental forming with some confidence.

Simulating FDM 3-D printing requires simulating the change in plastic
viscosity as it cools after coming out of the hotend, as well as the
changing tensions in the viscoelastic material as it stretches,
squishes, cools, and droops under the influence of gravity.
Layer-to-layer adhesion is a product of, among other things, the
heating of already-solid material by newly-deposited material.  If you
want to accurately predict the effect toolpath variations will have on
the mechanical properties of such parts, you need to simulate these
effects.

A particularly interesting class of techniques achieving prominence in
computer graphics in recent years are the "material point methods",
which are hybrid particle–field simulation methods capable of
achieving surprisingly visually convincing simulations of snow, cloth,
sand, water, hair, cracking mud, and other difficult materials, as
well as complex material interactions.  It's possible that the MPM
might make it possible to adequately simulate more complex material
dynamics during the construction process, such as the thixotropic and
frictional behavior of clay smeared by a spatula.  If not, perhaps
other numerical techniques would be adequate; for example, FEM with or
without adaptive remeshing or finite volume methods.

However, in order to make it practical to run an optimization
algorithm over your simulation algorithm, there are special
considerations.  One is that it needs to be fast enough to be run many
times.  Another is that, for many of the currently most successful
optimization algorithms, it needs to be *differentiable*, typically
using reverse-mode automatic differentiation.

Evaluation simulation E
-----------------------

Once you've simulated the thing being made, you need to evaluate the
simulated thing's simulated performance.  Perhaps you are interested
in its appearance from a certain angle, or the load it can bear over a
certain area, or its acoustic frequency response; literally anything
that can be quantified in simulation can be used for evaluation.

This simulation will often use different algorithms than the
construction simulation.  But it, too, is subject to the constraints
of rapidity and possibly differentiability.

Measurement M of fitness for purpose Q
--------------------------------------

Once you've computed the total performance evaluation, you want to
measure how *fit* that performance is for the purpose you had in mind,
reducing its badness (or equivalently its goodness) down to a single
scalar score, so that optimization becomes a meaningful concept.  This
may include a variety of factors; for example, bridge designs that
fail to span the gap, fall down, or block the river beneath might all
get very heavy penalties, much larger than bridge designs that merely
cost too much.

Optimization algorithms
-----------------------

Many high-dimensional optimization algorithms can be used.  The most
fashionable at the moment, due to their extensive development for
optimizing ANN parameters, are variants of gradient descent such as
Adam; but you can also use things like Nelder–Mead, genetic
algorithms, and the goofy hybrid of Nelder–Mead and the method of
secants that I wrote about in Dercuano, and probably also lots of
things I don't know about yet.

Some of these algorithms require the gradient of the loss function
with respect to the design variable vector P.

Many of these algorithms in their usual form require a space of
constant, finite dimensionality over which to optimize; depending on
the structure of the problem, it may be straightforward to add more
design variables over time.

It's worth noting that finding the *true* optimum is often unnecessary
and nearly always infeasible in practice.  The above algorithms
generally give a *close approximation* of a *local optimum*, but are
not guaranteed to be able to do even that.

There is an applicable generic optimization approach used in a number
of very interesting recent research papers from Disney Research Zürich
on the computational design of linkages, compliant mechanisms,
metamaterials, acoustic responses, and so on.  The process starts by
generating a database of random samples from a parameter space of 2–10
dimensions, characterized according to their properties of interest
(our E above).  Then, for each new design objective Q, the database is
searched; the nearest random sample is selected, and continuous
optimization algorithms such as gradient descent are applied to
generate the nearest point in the property space (E) reachable by any
design in the parameter space (our P, or perhaps C(P), since the
Disney group generally doesn't try to simulate the fabrication process
itself).  Sometimes multiple components from the database are combined
into a single design.  Sometimes additional optimization algorithms
are used, often in a new, higher-dimensional parameter space.

Tolerances
----------

Performance *predictability* or *stability* may be a crucial factor
for fitness: if a particular toolpath achieves very high fitness, but
toolpaths displaced by tiny errors achieve very low fitness, then
perhaps it is not a very good design.  The traditional way to handle
this in analog circuit design is by Monte Carlo simulation: running
many simulations with component values slightly perturbed in the ways
that they are known to be perturbed in real life, for example by
temperature or manufacturing variation, and with some noise added to
the input.  In this way we can distinguish predictably good designs
from designs that might be good once in a blue moon.

Affine arithmetic, interval arithmetic, reduced affine arithmetic, and
SAT solvers are alternatives to Monte Carlo simulation which may be
more efficient for a certain problem.

Incrementalization
------------------

A straightforward application of the above recipe requires you to
repeatedly revise P, re-evaluate C from scratch on it, re-evaluate E
from scratch on the result, re-evaluate M from scratch on that result,
then possibly do all of that again backwards and in high heels to
calculate the gradient of M with respect to P, and finally run a
optimization step.  But in most cases P is almost the same as a
previous P, so most of the answers will also be almost the same.

Caching-based incrementalization approaches, like Umut Acar's
"self-adjusting computation", can often provide speedups of five or so
orders of magnitude if P is *almost the same* in a very particular
sense: if most of its components have *no* change, but some of them
have *arbitrary* change.  Incrementalizing the procedure in this way
is not helpful for gradient descent as such, since very few of the
components of the gradient are ever precisely 0, but if we modify the
optimization procedure to search along only one or a few dimensions of
P at a time
("coordinate descent") — perhaps the ones whose component in the gradient is
largest — then we may be able to get a big speedup out of this kind of
incrementalization.  SKETCHPAD's constraint-satisfaction algorithm
used a relaxation approach somewhat similar to this.

(Self-adjusting computation and similar approaches also suffer from
some of the same time–space tradeoff difficulties associated with
reverse-mode automatic differentiation; indeed, the memoization store
produced by self-adjusting computation can be used directly for
reverse-mode automatic differentiation.  I suspect the usual
periodic-checkpoint approach to reverse-mode automatic differentiation
may be more difficult to apply to self-adjusting computation.)

(When doing coordinate descent with some kind of memoization, it may
be possible to speed the affair up by not memoizing the intermediate
evaluations during each line search or hyperplane search. Also,
updating the gradient incrementally would probably blow all the
advantages of incrementalization, so maybe you want to do that search
with successive parabolic interpolation or something.)

I think the self-validating arithmetic approaches mentioned above can
also offer, in some cases, an alternative incrementalization approach.
For example, we can calculate bounds on the possible values of M — and
all intermediate variables cached by a memoizing incrementalizer — for
values of P within a certain many-dimensional bounding box.  If a
gradient-descent step moves our estimate of P, but only a few of the
new components are outside the bounding box previously used, we can do
the computation incrementally as before, updating just those
components.

There are some loose ends there with respect to how big a bounding box
you pick, and when you decide to shrink it, but I think it's
tractable.

This hybridization of self-validating arithmetic with self-adjusting
computation is more general than just checking the inputs;
intermediate memoized values can also be checked to see if they are
within previously computed bounds, or can be made so by narrowing the
bounds on the new input value, and in this case the previously
memoized values can be used from there on.

Replanning during construction (automated improvisation)
--------------------------------------------------------

As the actual process of making a thing happens, new information comes
to light.  Perhaps some dimension was achieved to better-than-expected
precision, or a block of wood has less knotholes than feared, or a
piece of clay is drier than expected.  In these cases a fully general
response is to rerun the whole planning process, given the current (or
near-future) state as the starting point, in order to take advantage
of the new information.

At times it's necessary to plan out expectations which will permit the
original plan to continue to be followed.  For example, perhaps the
profile of light on a rotating clay object should be within certain
limits; if not, actuation should immediately cease, reverting to some
sort of "safe" or "home" position likely to do minimal further damage,
until a new plan can be formulated.  Less dangerous departures from
expected results may permit the original plan to continue while new
plans are hatching.

XXX anytime

Indirection in construction and design
--------------------------------------

A lot of the process of making things is indirect, to the point of
shaving the proverbial yaks.  Sometimes this indirection is necessary
to get the job done at all; at other times it merely improves
efficiency or quality.  Sharpening your knife or your wood-planing
blade every few minutes of cutting, or whenever they get dull or
nicked, will allow you to cut faster despite the lost time.  A form
tool on the lathe can often produce a particular contour much faster
than a single-point cutter can; grinding the form tool may save you
time.  Adding an assembly step at the end of a process can allow you
to stamp a product out of sheet steel instead of milling it out of a
billet, making it orders of magnitude cheaper.  A PLA FDM 3-D printer
can't make things out of sheet steel, but it can definitely print
press-forming dies for a sheet-metal brake, or beading dies for a
bead-rolling machine.

So it's worthwhile to keep in mind the possibility of *indirect*
construction, by constructing tools or parts that are then
used — perhaps many times — for the desired final product.

Using a single stamp or thread-cutting die or D-bit or mold or
whatever many times during the making of a thing implies that many
parts of that thing will be the same, which in some sense means that
your vehicle of indirection will be a compromise between the needs of
those different parts.  This compromise has a computational benefit,
though: it reduces the dimensionality of the space to be optimized.
Moreover, the design of that reusable part is potentially valuable for
other, unrelated designs, perhaps stored in a database like the Disney
Research Zurich linkages mentioned above.

The invention of reusable approaches that can be applied to many parts
of a design is not limited to physical tooling, though; things like
"gusset", "tube", and "truss" are commonly useful to reduce the mental
effort of mechanical engineering, and things like "differential pair",
"negative feedback", and "cascode" are commonly useful in the same way
in analog electronic design.

There's a hypothesis that the reason structures like bipinnate
compound leaves occur in totally unrelated families of plants (ferns,
mimosas, and fishtail palms, for example) is that they are
computationally simple to describe in some absolute sense.  But
another possibility is that they're simple to describe *in terms of
highly conserved plant genetic capabilities*.  With this in mind, you
could imagine optimizing not a specific toolpath itself but a sort of
"genome" or "program" to generate a toolpath — an indirection in the
design process analogous to the indirection of a reusable drillbit in
the construction process.  Doing this successfully will give a design
containing reusable parts, not just in the sense of actual immutable
parts such as a hinge but also in the sense of design tricks like
cascodes and gussets.

Experiment design and system identification
-------------------------------------------

Above I described C as a function of one variable: P is the design
toolpath, C(P) is the object or range of objects resulting from
executing that toolpath, and E(C(P)) is the performance of that
object.  But really C is also a function of the manufacturing process
and the materials' properties; we could say C(P, T), where T is this
description of the process and materials.

In some cases not enough is known about either the manufacturing
process or the materials' behavior in use — T, that is — to simulate
them with any confidence.  In such a case we have a different design
objective, one in some sense diametrically opposite to the
"tolerances" section above: we want to know the cheapest and quickest
toolpath that will reduce our uncertainty about the unknown variables.
So, for example, to shape something out of plastic clay so that it
will work, we would like to use a toolpath whose results vary as
little as possible over a wide range of plasticities, since the clay's
plasticity varies rapidly over time and in different parts of the
clay.  But, to find out what that plasticity *is*, we would like to
use a toolpath whose results vary *as much as possible*.  This is
perhaps in a sense the difference between science and engineering, or
exploration and exploitation in reinforcement learning, but we still
want the results to vary minimally with *other* unknown properties
such as ambient illumination, so that we can confidently interpret our
experiment's results.

To some extent it may be possible to mix such experimentation into the
construction process to support replanning; perhaps prodding the clay
a bit in a spot we will smooth over later anyway, for example, can
yield useful observations without affecting the final result.  In
general constantly adding a little bit of noise well within tolerances
can provide a "subliminal" experimental result of the effect of that
noise; to the extent that the phenomena involved are linear, we can
confidently extrapolate from these very small effects to much larger
ones.  The noise can even be much smaller than existing noise in the
system, only detectable by correlating over a large interval (for
example, imaging a large area of clay, or feeling a long plasma cut in
metal).  Impulse responses for a convolution reverb are sometimes
acquired from real spaces in this way by using white-noise excitation.

The simplest way to interpret the results of such experiments is to
use the same simulation-optimization process as used for design,
minimizing M(E(C(P, T)), Q); but now the toolpath P is fixed (it's the
experiment we performed), Q is our observations from the experiment, T
(the description of the process and materials) is the "design
variables" for the optimizer, and E and M are the observations we have
available and the probability of various kinds of errors and
corruptions in them, instead of real-world performance of a design and
how well that performance fulfills engineering requirements.

"Making things"
---------------

Although above I've focused on manufacturing, this approach is quite
generally applicable to control and design problems; the "things"
being made need not be physical objects.  In the cybernetics
literature approaches like the above are commonly called "optimal
control theory".