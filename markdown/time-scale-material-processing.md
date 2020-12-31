We frequently talk about whether something is soluble in water as
purely a function of temperature, because of course the equilibrium is
a function of temperature (and slightly of pressure, or more than
slightly, for gases).  But equilibrium is an ideal state never
reached; two different materials with the same equilibrium solubility
might have very different dissolution rates, and in the absence of
seed crystals, one might have a much higher energy barrier than the
other to nucleate crystals.  Moreover, the growth of crystals after
nucleation is initially exponential, then slows down to quadratic,
rather than the initially-linear growth you'd expect from the simple
Boltzmann energy-barrier picture.

Similar comments pertain to other reactions: the Gibbs free energy
determines the reaction's equilibrium, but not the reaction rate,
which may be autocatalytic (like crystal growth, but with diffusion)
and thus experience exponential growth.

Historically the humans haven't taken much advantage of this in
material processing (?), other than in heat treatment of metals, where
it's an unavoidable challenge.  But most lab techniques involve
running the relevant reactions fully to equilibrium over the time of
seconds to hours, spanning about four orders of magnitude.  Processes
that don't happen to an appreciable degree over hours are considered
unimportant; processes that happen in less than a second are
considered immediate.

It occurs to me that modern electronics and microfluidics each offer
us the opportunity to intervene reproducibly in such processes at
nanosecond timescales, adding nine more orders of temporal magnitude
to our arsenal.  If we have two processes in a material mixture, one
which runs to completion at a given temperature in 10 microseconds and
the other in 10 milliseconds, we can interrupt the proceedings after
10 microseconds when the second process is only 0.1% complete, or
perhaps 0.0001%.  (Interrupt?  For example, by diluting, chilling, or
poisoning the interaction.)  We commonly do this kind of thing over a
longer timescale in cooking: boiling the carrots for five minutes may
make them delightfully soft, while boiling them for an hour will make
them unpalatably mushy.

The information needed to plan such processes is rarely available in
the existing research literature, because workers in the field usually
don’t care whether a particular material change takes a nanosecond, a
millisecond, or a microsecond.

We can alternate between two processes, one which has a yield of 0.01%
of a desired product (due to equilibrium, for example) and the other
of which removes the product from it, at kilohertz or megahertz
frequencies.  Of course, there are many existing processes which work
this way already without such alternation; the Pidgeon process, for
example, produces magnesium through silicothermic reduction of
magnesia (produced by calcining dolomite) despite an unpromising
equilibrium, because the magnesium boils out of the reaction and the
silicon is taken up by quicklime (also from the dolomite) to form
larnite.  But there are other processes that cannot be run in this
way, for example because they involve ingredients that would have
unwanted reactions with one another, or the desired equilibria require
vastly different temperatures or pressures.

