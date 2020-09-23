One of the surprising results of the precipitous decline of
photovoltaic panel pricing (which has lowered the cost of energy in
sunny places), is that the tempered glass used to prevent the
paper-thin PV silicon wafers from being broken by hailstones, or rocks
thrown by rowdy teenagers, is now nearly as expensive as the PV cells
themselves.

Greg Sittler tells me that one solution to this is to protect the PV
panels with chicken wire, which absorbs some 10% of the insolation,
suspended some distance above them.  Chicken wire is tossed-rock-proof
and hailstone-proof, and typically withstands a few decades of
weathering.  Galvanized 20-gauge chicken wire is about US$2.90/kg
(from [wireclothman](https://www.wireclothman.com/shop.php?cPath=44)),
which is about US$1.30 per square meter for the “fine” 1-inch (25-mm)
mesh size, or about US$3 per square meter [from Ace
Hardware](https://www.acehardware.com/departments/building-supplies/gates-and-fences/chicken-wire/73101).
This seems like a very good solution to me.  (You might need two
staggered layers in order to reliably stop small rocks.)

Chicken wire is made of galvanized mild steel, which mostly deforms
plastically upon impact, thus being broken by a sufficient number of
repeated impacts.  However, its hexagonal structure is better suited
to absorbing impacts than a square mesh, because it’s inherently
stretchy, though not quite so stretchy as a knit-fabric structure
would be; a knit-wire structure would be able to absorb much more
impact energy for a given amount of wire by virtue of experiencing
more macroscopic deformation, just as a hexagonal mesh can absorb more
than a square mesh.  Alternative materials that occur to me include
music wire, nylon, PTFE, UHMWPE, rubber, polycarbonate, glass fiber,
basalt fiber, polyimide, polyamide-imide, glass-fiber-reinforced
polyamide-imide, and polyurethane.

The ideal material for this purpose would combine a high [mechanical
energy capacity] like rubber (2–9 kJ/ℓ), nylon (0.3–2 kJ/ℓ), or ASTM
A228 music wire (11–14 kJ/ℓ); excellent ultraviolet resistance like
steels, PTFE, glass fiber, and basalt fiber; and low price, like nylon.
Many of the polymers listed above can be heavily filled and mixed with
UV-absorbing and free-radical-scavenging components in order to
improve their UV resistance, but generally not to the decades of
endurance in thin fibers needed for maintenance-free PV operation.

[mechanical energy capacity]: https://en.wikipedia.org/wiki/Energy_density#Tables_of_energy_content

An alternative, then, suggested by Greg, is to combine virtues by
using fairly stiff but UV-proof fibers like glass fiber, mild steel,
or PTFE to form “trampoline panels” that are suspended around the
edges by springs made from a material with a higher mechanical energy
capacity.  These springs might be coils, knit fabric, cantilevered
leaf springs, zigzag fibers, foam blocks, or in some other shape, but
at any rate they can be protected from the sun, so they can be made of
cheap materials like nylon, polyurethane, or perhaps mild steel.

Both the springs and panels might need to resist creep, which might
require using a high-melting material rather than an organic polymer,
though teflon and polyimide might be good enough.  Also, though, it
might be the case that creep is not a real concern here, because the
normal relaxed loading scenario is a tiny fraction of what the
protective layer must be designed to tolerate during an impact.  So,
for example, according to Du Pont’s Teflon PTFE Properties Handbook,
at room temperature teflon creeps by 100% in a few hours under a
10-MPa load (close to its ultimate strength), but takes hundreds of
hours to creep by 1% under a 3.5-MPa load.

It might be helpful to use multiple different fiber sizes, like the
ripstop nylon used in parachutes.  A small rock of 10 mm diameter
might typically weigh 3 g and be hurled at some 15 m/s, thus carrying
some 300 mJ.  Stopping it within 100 mm thus requires at least 3 N of
force along its direction of motion.  Suppose it strikes a single
strand of the net, which deforms to catch it; then these 3 N might be
15 N in each direction along the strand, so the wire must withstand
some 15 N.

The fiber diameter needed to resist this rock depends on the material
chosen (for the trampoline panels, if those are used).  Teflon’s
ultimate strength is about 10 MPa; that of rubber, about 16 MPa,
depending on fillers; that of polyimide (Kapton), about 200 MPa; that
of mild A36 steel, about 500 MPa, though its yield stress is lower,
around 200; that of nylon, about 900 MPa; that of music wire, gel-spun
UHMWPE, or E-glass fiber, about 3 GPa; that of S-glass or basalt
fiber, about 5 GPa.

So, depending on the fiber chosen, you might need a fiber of 1.4 mm
(of teflon, suggesting that teflon may be too weak for this) or of
60 μm (of basalt fiber) to stop the small rock.

But consider a larger rock, 100 mm in diameter, weighing 3 kg, thus
carrying some 300 J of energy.  Stopping it in the same 100 mm
requires 3 kN of force, or perhaps 15 kN if it is being stopped by a
single strand, requiring a 44-mm-diameter teflon bar or a
2-mm-diameter basalt-fiber rope.  If the strands are 10 mm apart then
perhaps we can ensure that it strikes at least 27 of them (9 in each
of three basket-weave directions), so perhaps the load is only some
600 N each, requiring teflon fibers of “only” 8 mm diameter,
converting the rock shield into a very effective and expensive PTFE
sunshade, or 400-μm basalt rope.

So, the ripstop approach would be to make, say, 90% of the fibers
thin, weak, and cheap, to catch the small rocks, and the other 10%
stronger, either by making them thicker or using a stronger material.

The stronger “ripstop” or “reinforcement” cables can be thick enough
to carry a thick UV-protection layer, made, for example, of
carbon-black-filled teflon.  Even UV-protection fillers in a polymer
might slow the degradation of such a thick cable to a tolerable degree
during the panels’ design lifetime.

For example, you could use 0.2-mm (AWG 32, much thinner than usual
20-gauge 0.8-mm chicken wire) galvanized mild steel wire spaced 10 mm
apart, then 3-mm (AWG 8) music wire every 100 mm, which you would also
have to galvanize.  If the weave goes in three directions, this works
out to 300 m of thin wire (30 g) and 30 m of the thick wire (2 kg)
per square meter.

Unfortunately at onlinemetals.com, 0.125-inch music wire costs
US$15.79 per pound, or US$34.80/kg, 11 times the price (by weight) of
chicken wire; so 2 kg/m² is US$70 per square meter; while PV modules,
including the glass, currently only cost about US$40 per square meter,
so this is still too expensive.  (MSC offers a similar price; it’s not
just onlinemetals.)

Given that, though, I’m pretty sure it’s possible to relax the problem
to get the cost down to a reasonable level.

Probably what I need to quickly vet materials for such uses is a cost
per newton meter: the cost for a meter of cable thick enough to resist
a load of one newton.  Music wire at US$15.79/pound times 7.9 g/cc
divided by 3 GPa is about 90 microdollars per newton meter, while mild
steel at US$2.90/kg times 7.9 g/cc divided by 500 MPa is about 46
microdollars per newton meter.  (Maybe the bulk metal is cheaper than
chicken wire, though.)  [Nylon rope
costs](https://www.usnetting.com/rope/nylon/) US$81 for 250 feet of ⅜"
3050-pound-test braided rope, or in SI units, 76 m of 10-mm
1380-kg-test braided rope; that’s about 80 microdollars per newton
meter, but not UV-resistant.

[Amazon
suggests](https://www.amazon.com/Galvanized-Strand-Length-Breaking-Strength/dp/B00106J7NE/)
“Campbell galvanized steel wire rope, 7×19 strand core, 3/16" bare OD,
250' length, 840 lbs breaking strength” for US$61.92 (“+$209.82
shipping & import fees” to Argentina), which is 4.8 mm bare OD, 76 m,
3.7 kN, and is claimed to weigh 16.5 pounds.  From the weight
presumably the cross section is only about 12 mm², so as a solid round
steel rod its diameter would be 4.0 mm, the rest I suppose being air
between strands.  The strength would thus work out to 300 MPa, so
either it’s a bit underrated or the spool weighs a lot; one buyer
claims that the spools he’s tested broke at over 1000 lbs (4.4 kN),
which works out to 360 MPa.  Taking them at their word, though, this
works out to 220 microdollars per newton meter, not including
shipping; considerably pricier than the other alternatives.
