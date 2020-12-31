A dividing plate has circles of evenly-spaced holes used to measure
out precise divisions of the circle for machining purposes such as
cutting gear teeth; by using close-fitting physical contact between
hard materials with minimal thermal expansion (for example, a hole in
a steel plate and a brass dowel pin shoved into it), they can easily
achieve precisions far better than we can achieve by eye.  “Cliff” aka
“Clickspring” has [speculated that such objects might date back to
Hellenistic times or earlier][0], since you’d need *some* way to lay out
the gear teeth in the Antikythera Mechanism, and a compass (without
even a straightedge!) is sufficient measuring equipment to construct
them.  Modern dividing plates are normally used with a dividing
*head*, which gears down the angles by some constant factor to
increase the number of possibilities.

[0]: https://www.youtube.com/watch?v=BIUAdINXZmQ "’Antikythera Fragment #2 - Ancient Tool Technology - The Original Dividing Plate?’, May 21, 2017"

But if you had no gears and wanted to minimize the number of holes you
had to drill, and thus the opportunities to introduce error, you could
get by with a relatively small number of plates stacked on a common
axis.

To divide a circle into 6 equal sectors, you can use one plate with
two holes 180° apart and a second plate of the same diameter stacked
atop it with two holes 60° apart.  By aligning each of the four
possible pairs of holes in these plates with a dowel pin (several of
which seem to have been present in the Antikythera Mechanism, both as
gear pivots and as rivets), we achieve four orientations of the top
plate relative to the bottom, adding four positions to the two
achievable with only the bottom plate.  Even if both plates are
present, the dowel pin can stick through the top plate, so we can bump
our straightedge up against that dowel pin instead of whatever dowel
pin we have stuck in the top plate.  (The other side of the
straightedge might, for example, run through the center of the shaft,
as in Clickspring’s ingenious construction.)

A 120° plate would work in precisely the same way as the 60° plate.
You can think of the 120° plate as giving you the option to either add
or subtract 120° from either of the two reference angles (0° and
180°).

With the 180° plate and a 90° plate, again stacked with the same
diameter, we can divide the circle into 4 equal sectors.  If we then
use the 60° (or 120°) plate from the two 180° and two new 90°
positions, we can now divide the circle into the other 8 of 12 equal
parts.

Adding a fourth plate, again stacked with the same diameter, we could
increase this from 12 equal divisions to 36; the possible angles
between the two holes on the rim of this fourth plate are 10°, 20°,
40°, 50°, 70°, 80°, 100°, 110°, 130°, 140°, 160°, and 170°.

If at this point we wanted to continue in this balanced-ternary
groove, we would add an angle of 3°20' + 10°*n* for some integer *n*
and get 108 equal divisions of the circle, but for many purposes it
would be more useful to be able to divide the circle by multiples of
5, so a plate with *three* holes instead of two (at 0°, 2°, 4°, all
plus 10°*n*, thus allowing us to reach 2°, 4°, 6° (10° - 4°), etc.)
would be useful.

Note that at this point we are suffering from symmetry: although there
are three positions in which we can position this new plate relative
to the previous one, the center position of these three offers us no
additional dividing power.  We’ll come back to the theme of suffering
from symmetry below.

So at this point, for 180 equal divisions, we have five plates, four
with two holes each (plus the shaft hole) and a fifth with three
holes, for a total of 11 holes, or 16 holes if we count the shaft
hole.  A sixth plate with two holes brings us to 360 equal divisions,
6 plates, and 13 holes.  This is substantially simpler than drilling
360 precise holes into a plate.  It might be less convenient to use,
but when switching between angles you simply leave some of the plate
pairs immobile to drop out the factors they contribute, so you can
divide the circle by any of the divisors of 360: 2, 3, 4, 5, 6, 8, 9,
10, 12, 15, 18, 20, 24, 30, 36, 40, 45, 60, 72, 90, 120, 180, and 360.

(Perhaps the Babylonians and the Vedic sages stopped there because you
can’t construct a regular heptagon with a compass and straightedge.)

It might be desirable to use plates of different diameters, stacked
like the discs of the Towers of Hanoi, each one referenced to the disc
below it with a dowel pin at its rim.  At first I thought that to make
this work without increasing the number of plates, though, we’d need
more holes in each plate, since you can’t switch the “input hole”
referenced to the previous plate and the “output pin” the next plate
(or final output angle) is referenced to, so if there are only two
holes in the previous plate, there are only two positions for a given
plate, so with only two holes a plate always moves you either
clockwise or counterclockwise — you don’t get to pick.  So I thought
you needed three holes per plate, plus the center hole, and one of
them can have a reference pin permanently installed in it.

But then I realized you can *flip a plate over* if the reference pin
sticks out of both sides.  And that way you can either add *or*
subtract.  We were suffering from the reflection-plane symmetry of the
plates without even noticing it!

So, by this method, to get to 360°, you still need six plates, each
with a dowel pin permanently pressed into one of its holes, five with
two holes and a sixth with three, a total of 13 holes, plus the shaft
holes in the center.

Incidentally, the width of the dowel pin itself can be calibrated to
give us a particular angle, so we can double the number of angles
available by bumping our straightedge up against one side or the other
of the dowel pin.

But what if we go back to movable dowel pins all at the same radius,
and exploit this new possibility of flipping the plates over?

Our first plate, which I will assume is clamped down to a table or
something, has two holes 180° apart, as before.  Our second plate now
has *three* holes, at -60°, 0°, and +90°.  With these two, and the
possibility of flipping the second plate, we can reach 0°, 60°, 90°,
150°, 300°, 270°, and 210° from the 0° hole on the first plate, plus
180°, 240°, 270°, 330°, 120°, 90°, and 30° from its 180° hole.  So we
get to 12 equal divisions of the circle with only 2 plates, 5 holes,
and one dowel pin, instead of (as previously) 3 plates, 6 holes, and 2
dowel pins.  But maybe we could do better than this, because of our 14
configurations, only 12 are unique — we can reach 270° and 90° in two
different ways.

What do we gain from a third flippable plate with three irregularly
spaced holes?  We could use, for example, -10°, 0°, and +5°, or
perhaps some variant that spaces these out by some multiples of 30°.
This gets us to 72 equal divisions of the circle in 3 plates, 8 holes,
and two alignment pins.  I think we could still do better than this,
though, because the 15° increment here doesn’t buy us anything.

A fourth flippable plate with holes at -1°, 0°, and +2° gets us to the
traditional 360°, in 4 plates, 11 holes, and 3 alignment pins.

We could try to exploit the possibilities inherent in this scheme more
fully.  Suppose that our second plate, instead of having its holes at
0, -2/12, and +3/12 as before, instead has them at 0, -2/14 and +3/14?
As before, this allows us to measure 2, 3, or 5 divisions in either
direction from either of our two initial reference holes, which are
themselves 7/14 apart.  But this doesn't actually work the way we
hoped: instead of getting 14 equal divisions, we get only 10 distinct
positions, because we have two different ways to reach +2/14 (0 + 2/14
and 7 - 5/14), and simiarly for 5, 7, 9, and 12.  By trying to be less
clever, and putting the second plate’s holes at 0, -1/14, and +2/14,
we do in fact achieve an equal division into 14 parts with 2 plates, 5
holes, and 1 dowel pin.  If we divide the first plate into thirds
instead of halves, and put the second plates holes at 0, -1/21, and
+2/21, we can achieve an equal division into 21 parts with 2 plates, 6
holes, and 1 dowel pin.  Adding a third plate with holes at 0, -1/147,
and +2/147 gives us an equal division of the circle into 147 parts
with 3 plates, 9 holes, and 2 dowel pins.

All of this has a flavor rather similar to the note on the [6 Trit
Variac](6-trit-variac.md), but with angles rather than voltages.

If the plates are perfectly round and consistent in diameter, the
central shaft is strictly speaking unnecessary: you could line the
plates up by the feel of your fingers running over the edges.  This is
perhaps less implausible than it seems, since we know that lathe
technology goes back to Old Kingdom Egypt.

Metals are not the only reasonable materials for such discs, shafts,
and pins; jade would work well, as of course would various kinds of
concretes and sintered ceramics, perhaps even including fired clay,
particularly if foamed to improve its machinability.  Granite might
also be an option.  Glasses such as fused quartz would be more
challenging to cut without chipping, but might be feasible.

Tom Lipton of Ox Tools has demonstrated a modern alternative to
dividing plates, using two plates each containing an identical
circular row of identical bearing balls, which are pressed against one
another to give as many divisions of the circle as there are balls in
each plate.  The plates are constrained to move with the balls, rather
than rolling on them as in a ball bearing.  These balls are routinely
made spherical to submicron tolerances, and the errors that do exist
are averaged over the whole row of balls, permitting enormously closer
tolerances with this mechanism than with the holes bored in a
conventional dividing plate.

A sort of hybrid approach that avoids the use of shafts entirely would
align adjacent pairs of discs with kinematic ball-and-V-groove mounts
rather than entire rows of ball bearings or dowel pins.  Each disc
(perhaps except the bottommost) would have three balls on its bottom
side, spaced evenly 120° apart around its rim, and (perhaps except the
topmost) six radial V-grooves on its top side, in two sets of three
120°-apart grooves.  The angle between the two sets of grooves would
determine the contributions of this disc to the angle of the total
stackup.  So a single disc pair, where the bottom disc's six V-grooves
are all 60° from the previous one, could divide the circle into
sixths.  A third disc, with its V-grooves at 0°, 120°, 240°, 90°,
210°, and 330°, bumps that up to twelfths — but not 24ths, as you
might hope.  The third disc adds the possibility of incrementing the
angle by 90°, but not decrementing it, but since we already had 180°,
we don't need it.

There are a couple ways to try to improve that situation before adding
more parts or features.  If we put V-grooves on both sides of a single
disc, we *can* flip it over, giving us the possibility of either
adding its angle, or subtracting it, as initially — but without the
possibility of zero.  If we alternate double-V-groove discs and
three-ball discs (with the same three balls protruding from both side
of the disc) then we could delete a pair of discs from the stackup to
get a 0 angle, but at the expense of changing the stack’s thickness,
which may be a problem.
