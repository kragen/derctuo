How about a pure functional approach?  An image is, perhaps, a
function from (x, y) to (r, g, b), perhaps augmented with an aspect
ratio (max x?); an animation is a function from time to images; a
function is some code and some closed-over data; a graphical user
interface state is an image or, perhaps, an animation, and a function
from input events (such as mouse and keyboard events, but perhaps also
idle time and timer expiry) to new states.  Such definitions permit
caching, checkpointing, undo, rendering frames in parallel,
interrupting computations, and resampling, but no real composition ---
no way to provide a GUI state as a parameter to another GUI state.

The function to render a character-cell display is pretty simple:

    def pixat(x, y):
        row, xoff = divmod(x * cols, 1)
        col, yoff = divmod(y * rows, 1)
        glyph = font[text[row][col]]
        return glyph[round(xoff * font.height)][round(yoff * font.width)]

This is closed over variables `cols`, `rows`, `font`, and `text`.

If we've resigned ourselves to the cost of starting up and shutting
down a new "process" for each keystroke, mouse movement, and frame to
paint, a reasonable assembly-level interface for a machine-code
computation to access its input data is to map all the input and state
data "files" into a newly invoked process's memory space, one memory
segment per file.  Rather than identifying these segments by ordinal
number, I think it's better to identify them by textual name, and
expect the process to invoke a library function to look up the segment
descriptor --- like Unix environment variables, but each name is
associated with a whole memory segment rather than just a
NUL-terminated string.

For composition of computations with arbitrary machine code, we need
ways for a computation to produce more output than just an image and
take more diverse input than just keyboard and mouse events.  A
capability to spawn child computations --- write output files, in
effect --- would go some distance, but that only supports fanout, not
the much more ubiquitous fanin.  You need some kind of way to provide
an existing computation as an argument to another computation, and the
user interface affordances for this need to work in a more efficient
way than simply iterating over all computations that exist, querying
each one in turn.

A simple approach would be Golang-interface-like duck typing, where to
request an object as input you specify a list of method names (or
method type signatures) you want the object to support, and only
objects supporting all of these methods are offered to the user as
options.  In some cases these may just be things like "asString" or
"asImage".  To support backward compatibility, you might be able to
accept N different interfaces instead of just one.

A different way to do composition is using event channels: when an
event is posted to an event bus, all the subscribers on that event bus
are awoken with a copy of that event.  Usually this approach implies
some degree of nondeterminism; the Urbit approach is to wait on
(possibly remote) futures instead of on pub-sub event channels.  There
is still potentially some nondeterminism in the Urbit approach, since
in Urbit it is possible for a particular future to be satisfied by
more than one different process, and generally whichever one arrives
first is the one that wins.

A potentially more satisfying approach would be to make data files,
rather than stateful computations, the fundamental objects of the
world, but prescribe a FlatBuffers-like layout.