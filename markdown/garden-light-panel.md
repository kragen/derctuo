There are these solar garden lights for sale for US$1.15 or so which
contain a Ni-Cd battery, a monocrystalline solar panel to recharge it,
an LED, and a bit of circuitry to turn the LED on only at night.  The
battery in one of mine had died, so I took it out long ago, so I just
thought I’d do a little poorly-controlled MPPT measurement in a spot
of sunlight that comes in through the window here.

I disconnected the panel from the circuitry and hooked it up to some
resistors instead: 117Ω, 156Ω, 454Ω, and 988Ω, measuring respectively
0.60 V, 0.75 V, 1.91 V, and 2.09 V across the loads, and 2.25 V at
open circuit (loaded only by the meter), giving respectively 5.1 mA,
4.8 mA, 4.2 mA, and 2.1 mA, and 3.1 mW, 3.6 mW, 8.0 mW, and 4.4 mW,
suggesting that in this partial sun the panel’s maximum power point is
somewhere around 8 mW.

The panel is about 38 mm square, so in full sunlight it would receive
about 1.4 W of irradiation, and at the 21% efficiency expected for
monocrystalline panels it could produce 300 mW.  So 8 mW is pretty
low, and I should maybe repeat the experiment while actually going
outside and getting direct sunlight — maybe the spot of light through
the window was dimmer than direct sunlight by an order of magnitude or
more.  It’s also possible that this isn’t really a monocrystalline
cell but rather an amorphous cell.  Either way, it was nice to get
this 8-mW lower bound, because that’s already enough for a pretty
decent personal computer these days.

(I was using one of the shitty multimeters mentioned in [the note on
multimeter metrology](multimeter-metrology.md), which ought to give
all these figures an error somewhere around 1% or a bit lower.)

Even when loaded by only the meter, the panel’s voltage sagged when in
indirect sunlight, down to hundreds of millivolts, suggesting that it
would not be usable for indoor energy harvesting.

Trying to convert the above to a Thevenin equivalent gives internal
resistances for the panel of 320Ω, 310Ω, 82Ω, and 83Ω respectively,
suggesting that either the sunlight conditions changed as I was taking
readings or the panel is very far from being ohmic.

As a [rebraining](rebraining.md) candidate the garden light is
somewhat appealing: it has a built-in energy-harvesting power source,
and the cylinder containing it, the battery cartridge, and the
electronics is mostly empty, easy to open (three screws), 30 mm high,
and 70 mm in diameter.  This provides lots of potential space for
stuffing electronics into.  What it lacks is much in the way of I/O
devices, possessing only a white LED.

A potentially more appealing approach is to carefully remove the PV
cell and graft it into something else, maybe something easier to
carry.

In really full sun, I got 2.47 V open-circuit and 2.10 V across a 105Ω
load, thus 20 mA, 42 mW.  This suggests I’m actually not at full max
power and could benefit from going to a lower load impedance: the load
voltage is more than half the open-circuit voltage.  If the panel had
an ohmic internal resistance dropping 370 mV at 20 mA, it would be
18.5Ω.

However, I think we can be reasonably sure that, although that
“internal resistance” won’t remain constant, it won’t go *down*.
Which means that the maximum power output available in this sunlight
would be (½ 2.47 V)²/18.5Ω = 82.4 mW.  So I can squeeze at most
another factor of 2 out with lower impedance, so this panel is at best
5.7% efficient.  It must be an amorphous panel, not monocrystalline
like I thought.

On MercadoLibre there’s a 22mW 22mm×7mm panel for US$4 (crystalline)
and a few 1000mW panels in the range of 110mm×60mm for US$3 or so
(also crystalline).  But there are also 310-watt panels for US$82,
which is US$0.25 per peak watt rather than US$3.  And there are
(supposedly) solar calculators under US$1, but those have much smaller
cells (when they’re not totally fake).  So I think this is largely a
question of transaction costs, and these garden lights are probably
the cheapest way to get solar cells in the 40–60mW range.
