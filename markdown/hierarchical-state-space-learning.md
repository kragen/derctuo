(Probably none of this is new and all of it is obvious to those who
study such things, but I’m just beginning to learn about the area.)

Suppose you can observe a time-varying set of inputs or stimuli to
some system, like a stirred vat of reagents or a circuit or a
vibrating string, and a corresponding time-varying set of outputs.
How would you go about automatically “learning” the behavior of the
system?

If you suppose that the system is approximately linear and
time-invariant, then the output vector *y<sub>t</sub>* is given by
some direct-coupling matrix *D* multiplied by the input
*u<sub>t</sub>* plus some output matrix *C* multiplied by the system’s
internal state *x<sub>t</sub>*: *y<sub>t</sub>* = *Du<sub>t</sub>* +
*Cx<sub>t</sub>*.  Unfortunately we cannot observe *x<sub>t</sub>*
directly, and we may not even know what its dimensionality is, and it
may be infinite.  But by hypothesis it is evolving in time according
to some input forcing matrix *B* and its own square matrix of internal
linear relationships *A*: *x<sub>t</sub>* = *Bu<sub>t</sub>* +
*Ax*<sub>*t*-1</sub>.  And we can linearly superpose effects from
stimuli at different points in the past, so *y<sub>t</sub>* =
*Du<sub>t</sub>* + *C*Σ*ᵢAⁱBu*<sub>*t*-*i*</sub>, *i* > 0.

If we factor the feedback matrix *A* with an eigendecomposition
*QΛQ*⁻¹, the eigenvectors *Q* give us the “vibrational modes” of the
system, and the eigenvalues *Λ* tell us how fast they decay (or,
possibly, grow).  We can fold the *Q* and *Q*⁻¹ matrices into the *C*
and *B* matrices respectively to reduce *A* to the diagonal matrix of
eigenvalues.  Some of these eigenvalues may be small, which means that
the vibrational modes in question die away very quickly; unless their
coupling to the input and output is particularly strong, these can be
dropped, reducing the dimensionality of the model, with little effect
on the error.

Given some estimates *A*<sup>?</sup>, *B*<sup>?</sup>,
*C*<sup>?</sup>, *D*<sup>?</sup>, of the matrices *A*, *B*, *C*, and
*D*, we can compute a residual *y<sub>t</sub>* -
(*D<sup>?</sup>u<sub>t</sub>* +
*C*<sup>?</sup>Σ*ᵢA*<sup>?</sup>*ⁱB*<sup>?</sup>*u*<sub>*t*-*i*</sub>)
that tells us how shitty our estimates are for a given time *t*, and
then we can summarize this residual vector over all times *t* by, for
example, summing the squares or absolute values of the residuals to
give us an overall residual shittiness to minimize.  There are a
variety of standard optimization algorithms that may work to minimize
this residual.

In particular I think that if we use the sum of the squares of the
residuals, ordinary least squares may work: we just take the
derivative of the whole residual expression, set it to zero, and
figure out what values of *A*, *B*, *C*, and *D* that gives us.  If
there are less degrees of freedom in the observations *y<sub>t</sub>*
than there are in the four matrices we’re trying to estimate, I think
the problem is underdetermined.  For example, if we have single scalar
observations at 100 points in time *y<sub>t<sub>* for a scalar-input
system (i.e., *u<sub>t</sub>* is also one-dimensional), and we suppose
that the state space *x<sub>t</sub>* is 10-dimensional, then *A* has
100 degrees of freedom, *B* has 10, *C* has 10, and *D* has 1, so the
system isn’t fully determined.  But if we suppose that it is
9-dimensional, it is now fully determined, and if it’s 8-dimensional,
it’s overdetermined.  (In the underdetermined case, we could add extra
penalty terms for, for example, norms of the matrices involved, to get
the “simplest” solution in some sense.)

XXX okay smart guy, how can you derive 100 unknowns from a single
equation?  you add all the squared residuals together, take the
derivative, you have a sum of squared residual derivatives, you set it
to zero, that’s still one equation.  ohhh: you take the partial
derivative with respect to each design variable (*A*<sup>?</sup>₀₀,
etc.), and that gives you N equations — but now we’re faced with a
different problem... I guess I need to go back and brush up on OLS and
linear regression!

Another approach is to use gradient descent: if we initially suppose
that *A* is very small, perhaps a single real or complex coefficient,
we should be able to iteratively converge very quickly.  If we then
add dimensions one by one, optimizing after the addition of each new
dimension, we ought to be able to converge relatively quickly, as each
new dimension is in some sense being used to account for the error in
the previous approximation.

Since, as explained above, we can assume without loss of generality
that *A* is a (complex) diagonal matrix (of eigenvalues), the number
of design variables we need to optimize doesn’t actually increase
quadratically with the dimensionality of the state space, as I said it
does above — it only increases linearly.  For example, if we have 3
scalar inputs, 2 scalar outputs, and 20 hidden variables in the state
space, then *D* is 3×2, *A* is a 20-item diagonal matrix, *B* is 3×20,
and *C* is 20×2.  I think *B* and *C* need to be complex as well, not
just *A*, but *D* can be purely real.

It should also be possible to use L1 basis pursuit algorithms in order
to handle massively underdetermined systems, where we assume an
absurdly large number of state-space dimensions, but privilege
extremely sparse solutions.  This might require abandoning the
eigenvalue-diagonal assumption about *A*, because, although that’s the
sparsest *A* can be, it might impose unreasonable constraints of
non-sparsity on *B* and *C*.

Of course, no real system is perfectly linear, so even without
measurement noise, we’ll have a nonzero residual signal, but hopefully
we’ll also have some kind of estimate of the internal state vector of
the system at each point in time, which will presumably be
uncorrelated with the residual once our optimization has converged.
But if we take the outer product of that state vector with itself, we
will get a larger matrix some of whose items may have significant
correlations with one or another channel of the residuals, and this
will give us a second-order correction to apply to our linear
predictor.  The kernel trick used in support vector machines is, as I
understand it, a more efficient generalization of this approach, and
can be applied to learn more general system behaviors.

While you could in theory apply this sort of approach to any black-box
system at all, it won’t work very well for extremely nonlinear systems
like flip-flops.  For things like that, a hidden Markov model is
probably a better sort of model, and that also has efficient
algorithms for learning it; you can combine the two models by having
different *A*, *B*, *C*, *D* matrices for different Markov states.
But for mostly linear systems, this linear-first approach might have
some useful merits.

You can apply this to control systems (automatically tuning the model
for model-predictive control to the plant), simulation (the
plucked-string model alluded to earlier), or system identification
(guessing what kind of circuit you’re looking at), among other things.