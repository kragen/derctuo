In topology optimization, you typically design a structure for maximum
rigidity by beginning with a block of fog and then using gradient
descent to minimize a penalty function which has a few different
terms: one for the structural property of interest (such as rigidity
under a given load), one for regularization of the problem to rule out
physically-unrealizable checkerboard solutions full of
discontinuities, and a third fog-penalty term which forces the
elements toward 100% density and 0% density and away from 50% density,
again preferring physically-realizable models.

What if you apply the same approach to bits?  Suppose, for example,
that you want to find the representation of an integer *n* in binary.
You could start with 32 real numbers initially set to 0.5, then use
gradient descent or something to optimize |2⁰*v*₀ + 2¹*v*₁ + 2²*v*₂ +
... + 2³¹*v*₃₁ - *n*|² + αΣ*ᵢvᵢ*²(1-*vᵢ*)², for example.  This
function's only zero (for real *vᵢ*
and positive α)
should be the correct binary
representation of the number.  At all other points it takes on
strictly positive values, and it's differentiable everywhere.
Moreover, although I haven't looked, I think it's convex, so its only
local minimum is the global minimum.  So it should be tractable for
gradient descent.  Certainly gradient descent with random restarts
should always solve it, though if the random restarts are actually
required then maybe it would take an exponentially large time for such
problems.  Genetic algorithms should have no trouble solving it in a
reasonable amount of time.

Now, although it's I hope at least highly plausible that the above
approach will work for such a simple problem, think about more
interesting Boolean functions.  For example, given a binary
multiplication algorithm, the above approach can probably do division,
or, more interestingly, a square root.  Can it do LDPC decoding?  How
about inverting other less tractable functions on bitvectors, like a
round of SHA-256?  If you write down some inputs and outputs of a
branch-free block of instructions for some CPU, and express the
execution of a few arbitrary instructions as a similar Boolean
function of those instructions' bits, can it do superoptimization?

Purely agnostic approaches like this — perhaps we should say
"knowledge-free" or "ignorant", or "assumption-free" if we want to use
a euphemism — will surely be inefficient for many problems, even if
they can solve them at all.  Suppose we train a neural network on the
distribution of plausible solutions, as explored by Lunz, Öktem, and
Schoenlieb for inverse imaging problems: as we apply ad-hoc penalties
in topological optimization to solutions containing fog and
singularities, we can train various kinds of neural networks to
recognize the structure of plausible solutions, using them to penalize
unlikely solutions, such as superoptimized code containing invalid
instructions.  This way our search can probably converge much more
quickly than a purely ignorant search that doesn't know a
multiplication from a superoptimization.
