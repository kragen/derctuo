The idea of reproducibility I want to base Derctuo on requires some
explanation, since there isn't anything else out there that aims at
this, as far as I can tell.

The objective of Derctuo's virtual-machine design is that running the
same program with the same inputs always reproduces bitwise-identical
outputs, unless it fails; that this should be the case even when
executed on independent cleanroom reimplementations from the
specification, whether this year or in 300 years, on the same hardware
or different hardware; that implementing the virtual machine from the
spec should require only a few hours of work; and that this virtual
machine should be sufficient to reproduce all the computations I think
are interesting.

