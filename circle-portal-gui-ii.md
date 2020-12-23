In Dercuano I wrote about a "circle portal GUI" ZUI design consisting
entirely of circular windows.  Your user interface is a circular
viewport onto an infinitely zoomable canvas, which contains other such
circular viewports onto other parts of itself, each possessed of only
a position and size, a destination position and size and orientation,
a z-order, a background color, and a translucency.  You can create
these portals, duplicate them, move them, move their destination,
resize them, resize their destination, change their background color,
change their z-order, and follow them — they function as hyperlinks.
Also you can "snap" them, destroying them and copying the region of
canvas they view onto the place where they are displayed.

By arranging some empty portals with a given background color
somewhere, you could create a letterform, and then by making portals
that view that letterform in various places you could make copies of
the letter.  By arranging dozens of such letters together in a font,
you could have a way to write text in that font; if you made a "font
portal" viewing that font, you could make portals onto the positions
of letterforms in that portal, which could also be used to spell text;
by changing the destination of your font portal, you could change the
font used for that text.

(You'd probably want to have a way to interpose a level of indirection
like that after the fact.)

If you wanted to draw something in some changeable colors, you could
do that by putting your palette in one place, and making your drawing
out of portals onto the palette.

So this single type of object serves as a graphical primitive (since
you can set the background color), a limited IFS generator (without
shearing or nonuniform scaling, since each transform only supports
four degrees of freedom rather than the usual 6), a hypermedia
navigation system with live preview, a graphical instancing system
capable of some use as a stylesheet, a universal "view source" button,
and so on, all through direct manipulation, though a sort of direct
manipulation that would probably be super confusing, just due to its
hall-of-mirrors nature.

(I implemented a tiny prototype of the system one day, but didn't get
far enough to get a good sense for how to use it.  I imagine you'd
want to add some other graphical primitives and interaction modes;
dragging letterforms one by one is maybe not a great way to write
text.)

I was thinking today that it would be interesting to do the same thing
in 3-D, using spheres instead of circles.  The sphere portals could
display what was *within* their target volume, like you might imagine
looking into a crystal ball; they could display what was visible by
looking *through* it (either in a given direction, or in the direction
you're looking in); or they could display what would be *reflected
from the volume around* their target volume, just as a silver ball
displays what is reflected from the volume around itself.  This last
item would have the benefit that the target volume could itself be an
object in the world, so the hypertext links in this system could be
bidirectional, which might actually help a little bit with the
confusion, though it would impede uses like instancing a character
from a font.  (You could probably get an acceptable approximation by
using really small target spheres.)
