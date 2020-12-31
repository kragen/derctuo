Espacio de César posted a video demonstrating how to make a usable
40-watt soldering gun out of a heavy mains transformer (rewound with a
low-voltage secondary) and some heavy steel wire, saying he was
looking for about ½ volt on the secondary.  This seems like a very
reasonable strategy to me but I can’t salvage
transformers — invariably they are already recycled before I encounter
a discarded electronic item.  Probably even something like an ATX
power supply would be easier to find.

½V and 40W is about 80 amps, though, which is a bit more than ATX
power supplies can usually provide.

That also implies about 0.006Ω. Is that about the right resistance?
[Iron’s resistivity at room temperature][0] is about 100 nΩm; guessing
that César’s heating element is about 100 mm long and 1 mm², we get
0.01Ω, so yeah, that’s about right.  [1010 carbon steel is about 143
nΩm][1], while stainless is several times higher at some 700 nΩm.
Nichrome would be much better, at 1100 nΩm, and I’ll probably find
some sooner or later, since people constantly throw out broken hair
dryers and space heaters.

[0]: https://en.wikipedia.org/wiki/Electrical_resistivities_of_the_elements_(data_page)
[1]: https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity

We could get the same power out of a thinner wire at a higher voltage
and lower current, but at more risk of burning the wire out.  The
[wire fusing current estimates from Powerstream][2] that I used in
file `balcony-battery` in Dercuano suggest that 86 A is *already*
enough to melt 11-gauge iron wire (2.3 mm), 43 A is enough to melt
15-gauge iron wire (1.5 mm), 21 A is enough to melt 19-gauge iron wire
(0.9 mm), and 10.7 A is enough to melt 23-gauge iron wire (0.57 mm).
So really César is already past the edge of safety and will melt his
soldering tip if he holds the trigger down long enough.

[2]: https://www.powerstream.com/wire-fusing-currents.htm

Can you do it with simple electronics instead of a transformer?

You can’t just PWM the AC line current through a 6-milliohm heating
element; you’ll trip the house’s circuit breaker, and even if you
don't, you're dropping the line voltage to zero temporarily, and other
nearby appliances won't like that.  But you ought to be able to PWM it
into a hefty inductor with a hefty freewheel diode or ten, at least if
they have enough ballast to prevent thermal runaway.  Very crudely
guessing, if you have a duty cycle of 0.01% or more and a PWM
frequency of 10kHz, then your inductor just needs to prevent the
current from rising to too much more than 80 amps in 10 ns, or 8
billion amps per second, at less than 340 V (the peak voltage).  That
only requires 43 nanohenries, which you might get without asking for
it.  But it also requires subnanosecond switching times for that
current.  Also, you need an input capacitor bank that can handle 80
amps of ripple current, which is doable but nontrivial.

You probably *could* PWM the AC line current into a high-frequency
stepdown transformer, which could handle the 40 watts or whatever in a
much smaller core.  This is basically a flyback supply I think, just
with a stepdown instead of a stepup?  I don’t know, I have to think
about this stuff later.