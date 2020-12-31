Consider `5V-{4n7F-2k2||2.2mH(2%)-npn(B=50)[1k-G]}-G`.  This describes
an analog circuit, kind of a stupid circuit, but a circuit, with five
components.  In Falstad’s circuit simulator’s save format, such a
circuit would take about 190 bytes, but here it takes 42.  Moreover
you could sort of imagine that such a representation provides a sort
of key command interface; it takes me about 20 seconds to type it, and
that’s tremendously faster than I think anyone can click through all
the stuff in KiCad or Falstad’s simulator or LTSpice to do the same
thing.

I think it’s probably worthwhile building something that
simultaneously maintains this representation and a 2-D schematic
representation.

(For human readability it might be better to say “gnd” rather than
“G”.)