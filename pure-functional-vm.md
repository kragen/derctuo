I'm trying to figure out how to specify reproducible computations for
Derctuo in a way that won't cost me months of work before I can start
using it.

Urbit approaches the problem of doing reproducible computations with a
pure functional virtual machine rather than an imperative one.  This
rules some things out of scope: issues of efficiency, memory usage,
and latency, for example.  But it certainly simplifies the kind of
computation whose purpose is to compute an unknown result, rather than
to react to events in the world.  And it might be possible to make it
fast enough on modern machines, at least under most circumstances.

There's lots of information out there about how to do reasonably
efficient evaluation of λ-calculus expressions, and I've done a few
compilers along those lines myself.  My Bicicleta work instead uses
Abadí and Cardelli's ς-calculus as the basis, which is slightly more
verbose than the λ-calculus but, I think, considerably more convenient
for programming.  Using name-value pairs rather than positional
arguments to pass data around permits decentralized extensibility.

The Bicicleta interpreter that I wrote, however, is extremely slow,
close to the speed of bash script.  I'm sure I can do better than
that, using approaches like those I used in Ur-Scheme.