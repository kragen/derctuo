I've been watching a lot of videos of people explaining and
demonstrating how they machine metal parts with modern CNC lathes and
mills, as well as more exotic tooling like wire EDM and SLS machines.
It occurred to me that they're still mostly not taking much advantage
of the possibilities of what CNC machines could do.

First, a lot of vertical milling is done with cylindrical endmills.
Cylindrical endmills have to be unreasonably long and slender in order
to be able to reach a reasonable depth.  For a given end diameter,
tapered endmills offer a much better tradeoff of reach versus rigidity
than cylindrical endmills do.  But they have the disadvantage that,
with three-axis milling, they don't permit milling vertical walls.
But that's obviously fixable with five-axis milling.

However, it's even possible with four-axis milling, if your axes are
Y, Z, A, and B.  The X-axis can be fixed at the center of rotation of
the B-axis; it need not move.  This also eliminates the heavy and
finicky serial-kinematics prismatic joint upon prismatic joint of a
standard gantry, which I think may be a leftover from manual
machining.

We pay way too much for rigidity.  Machine tools are
conventionally built out of iron and steel, which are pretty rigid,
but also pretty expensive --- even to buy, but especially to shape.
Other materials are nearly as rigid and a hell of a lot cheaper, so
you can use far more of them.  Above I mentioned granite, but other
candidates include concrete, brick, and even plaster.  Building
machine tools out of concrete is a fascinating and underexplored area.

Aside from that, I think rigidity is perhaps overrated.  Hermle is
building their best machines, not out of granite, but out of a
granite-epoxy composite, as I understand it because it damps vibration
better.  In manual machining, rigidity (and taking up the backlash)
was the only way to get an accurate reading on where your cutting tool
was relative to the workpiece, because you didn't have any feedback
--- the machinist might not be running open-loop but the machine tool
was.  Nowadays we could use closed-loop feedback on relative
tool-workpiece positioning, which would also stop a lot of crashes,
but we don't.

When you're filing a part by hand, the file is held in your hand,
which is about a hundred times more compliant than the floppiest
machine tool frame.  But, if you hold the file firmly, it cuts cleanly
and doesn't chatter; and you can file your parts down to single-micron
tolerances if your micrometer is that good.  That's because you're
damping chatter instead of just resisting it, and because you're using
closed-loop feedback on when you're cutting, when you're not, and how
much you've cut.

The standard cure for chatter in a machine tool is to add rigidity: to
your setup, to your tool, to the tool frame, whatever.  But increased
rigidity doesn't eliminate vibrational modes; it increases their
frequency, decreases their displacement, and increases their force.
What *eliminates* vibrational modes is nonlinearity, like the
viscoelastic behavior of your hand meat on a file, or --- ironically
--- metal parts banging together and moving energy from a
lower-frequency vibration to a higher-frequency vibration.  The more
rigid and linear a system is, the higher its Q factor!

So I'd like to see more about other approaches to chatter that don't
depend on rigidity.  Damp vibrations with sand and gravel.  Actively
cancel chatter with piezoelectric actuators instead of passively
resisting it.  Cut with files with randomly-spaced teeth, perhaps made
with carbide inserts.  I don't know what will work.

As for closed-loop feedback, it's possible for interferometric systems
like ERIM's HoloMapper from 1997 to get submicron measurements at
millions of pixel locations across the surface of a part at once,
without making contact.  (At the time the latency was four minutes,
but there's no reason it needs to take that long now.)  Using this in
real time as you're machining would mean sacrificing flood coolant,
but modern carbide tools can cut steel pretty well dry.

I've previously written about geometric-optics sparkle feedback, where
a sparkle pattern from sparkle glued to a rigid body indicates
simultaneously its position and attitude to a camera at a known
location with a point-source light at a known location.  Combined with
a reference mask that obscures some of the sparkles, this should be
capable of giving relatively precise feedback, 

sparkle feedback

kinematic mounts plus clutches
