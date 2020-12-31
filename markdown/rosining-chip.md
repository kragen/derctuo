In [Peter Laackmann and Marcus Janke’s “Uncaging Microchips” talk][0],
at 30'20" they presented an approach I hadn’t heard of for removing
epoxy, the most common encapsulant, from a microchip package.  By
heating ordinary rosin or colophony to its boiling point of 320–360°
with a heat gun (which also is not a temperature I knew rosin would
withstand without charring) you can dissolve the *cured epoxy package*
in under 20', then clean it with acetone at 40°, though I suspect
alcohol might work as well, since what must be removed at that point
is mostly colophony.  Reportedly it smells terrible and leaves the
chip unusable because it loses the bond wires.

[0]: https://www.youtube.com/watch?v=pIpxawdUb4I

They also mention that chloroform, dimethyl formamide, and DCM can
swell or dissolve epoxy, so that you can “brush it away”, as they say,
although I would rather not be around any of those; and you can use a
CNC milling or grinding machine with micron precision; and you can
burn it with a laser, especially a 10-micron infrared laser to which
the silicon is transparent.

I suspect you could probably burn off the epoxy with a non-thermal
oxygen plasma as well; the epoxy’s reaction products with oxygen will
be gaseous at room temperature, while the reaction products with the
bulk components of the chip — aluminum, silicon, copper, silica,
hafnia — either don’t exist or are solid.  Maybe a non-thermal steam
plasma would also work, because although silane is a gas at room
temperature, it’s not very stable.  And of course ionization of air
generates oxides of nitrogen, which are of course well known as a way
to decapsulate epoxy-encapulsated chips; the talk above says you
usually need several grams of them.  See [the note on cold
plasma](cold-plasma.md) for more.

The rosining process is pretty interesting to me not only for seeing
the chip — for example for reverse engineering — but also for the
possibility of converting a packaged chip into a WLCSP, since WLCSPs
are usually hard to buy, especially in quantity 1.  The chip would
need to survive its rosining, but I don’t think 360° for 20' is enough
to cause substantial dopant diffusion; I think it’s just a question of
replacing the broken bond wires.