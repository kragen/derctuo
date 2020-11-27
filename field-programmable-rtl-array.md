What if, instead of individual registered LUTs, you programmed a
synchronous register-transfer-level machine whose individual units
were more like an ALU, or a GreenArrays x18 core, than like a gate?

I'm thinking of the TECS NAND-to-Tetris Hack CPU, based on the DG Nova
and the PDP-8, where individual bits in ALU instructions control
various aspects of the ALU instruction; and about the kinds of
register machines used for Bresenham line drawing and circle drawing
(Appendix E of Ivan Sutherland's SKETCHPAD dissertation, republished
as UCAM-CL-TR-574, describes these as "incremental computers" and
proposes using them to draw general conic sections); and about the
very simple register machines provided by AVR peripherals, for example
auto-incrementing timers that are connected to digital comparators
that automatically reset them, or analog comparators that can register
a timer value when they observe a transition; or Babbage's Difference
Engine, which tabulates a new value of an arbitrary polynomial on
every clock cycle, simply by adding to each register the difference
from the previous register; and so on.

In the Hack, the ALU field has 6 control bits, zx, nx, zy, ny, o, and
no, and the ALU is basically just six muxes:

    a <- zx ? 0 : x
    b <- zy ? 0 : y
    c <- nx ? ~a : a
    d <- ny ? ~b : b
    e <- o ? c+d : c&d
    output <- no ? ~e : e

This gives you 64 ALU instructions, many of which are unimplemented in
the official Hack simulator, including NAND, NOR, AND, OR, addition,
two's-complement subtraction, two's-complement negation,
one's-complement negation, abjunction, X+Y+1, and a number of others.
The [PDP-8's similar
setup](https://github.com/johnwcowan/pdp8x/blob/master/arch.md)
includes bits for bitwise rotation and byte swapping, which among
other things makes division much easier to implement.

Now, in a "FPRTLA", instead of routing individual bits around your
machine, you could route bytes, or words of 4-64 bits — an 8-bit byte
suffices to crossbar four inputs to four outputs with each output
derived from a single input, and those inputs and outputs can be full
words.

You could imagine that each cell in a rectangular array of cells, for
example, might be configured with, for example, such a 6-bit ALU
opcode determining its output word as a function of its two inputs, a
bit determining whether its output is registered or combinational, and
6 bits selecting its two inputs from among eight available inputs
(including its own output).  This might suffice for very simple
computation, but so far it's lacking the conditional routing and
conditional increment abilities needed for things like packet routing
and Bresenham lines.

One simple way to provide that would be to provide *two* configuration
words for each cell, and some kind of rule to choose between them,
perhaps based on a single bit from somewhere.

Without the possibility of combinational output, you could implement
such a machine much more densely in a bit-serial format — each 4×4
crossbar would literally have only 8 data wires going in and out of
it, plus 8 control wires.  This would of course be much slower.

If such a 4×4 crossbar had 16 control wires going in instead of 8, it
could perhaps implicitly perform a wired-AND or wired-OR, for example
by using open-drain transistors and pullup resistors; this might
eliminate some of the necessity for ALU operations.

An interesting question is what the minimal uniform unit cell for such
a machine might be.  It takes two or more inputs of some variable
word size, generates one output, and has some configuration data.
What's the simplest state machine that gives us a given form of
universality?

"Incremental computers"?
------------------------

In Sutherland's thesis, in perhaps the first proposal for a GPU, which
would later return to him as the "Wheel of Reincarnation" paper, he
said:

> In the course of the work with Sketchpad it has become all too clear
> that the spot-by-spot display now in use [is] too slow for
> comfortable observation of reasonable size drawings. Moreover,
> having the central machine compute and store all the spots for the
> display is a waste of general purpose capacity...
>
> *The technology of incremental computers is well developed*
> [emphasis Derctuo], but so far as I know, no one has yet applied
> them directly to the problem of computer display systems. Basically
> the incremental computer works by adding one register to another
> successively and detecting any overflows or underflows which may be
> generated. Certain registers are incremented conditionally on the
> result of overflow or underflow generation.

He goes on to explain something similar to the Bresenham line drawing
algorithm, using an "X increment" register for the fractional part to
be added on each iteration to an "X remainder" register, with an "X
scope" register being incremented on overflows, we can increment "X
scope" on average every 1/"X remainder" clock cycles.  Essentially "X
scope" and "X remainder" are the integer and fractional parts of a
fixed-point number (in Sutherland's case, binary), and "X scope" is
used to control the X position of a CRT beam.  A similar Y arrangement
is used for the Y beam.  The Clock of the Long Now does its
calculations the same way, bit-serially, using mechanical binary
computation.

This is labeled "Figure E.1. DDA for drawing lines," and it turns out
that this is an abbreviation for "[Digital Differential Analyser][2]",
the term more commonly used in the US at the time; "incremental
computer" was the term used in Britain, according to Charles Philip
Care's 2008 Ph.D. dissertation, "[From analogy-making to modelling:
the history of analog computing as a modelling technology][0]."  The
differential analyzer was the major invention of Vannevar Bush, who
also invented hypertext and headed the atomic bomb research program;
it was a mechanical contrivance that performed "numerical" integration
of ordinary differential equations, originally conceived by Kelvin but
not built successfully until Bush managed it in 1931.

[0]: https://core.ac.uk/download/pdf/47252.pdf
[2]: https://en.wikipedia.org/wiki/Digital_differential_analyzer

(Care's dissertation, though I disagree with most of its conclusions
and think its author misunderstands fundamental aspects of the
technologies he is attempting to chronicle, is one of the few pieces
of research to investigate the distinction between "analog" and
"digital" in a serious way.  The fact that the conclusions he arrives
at are wrong is, by comparison, less significant.  And it's one of the
few places that will tell you what "incremental computer" meant at the
time of Sutherland's dissertation.)

Presumably thanks to Sutherland, [nowadays the term "digital
differential analyzer" commonly refers to the algorithm he was
describing][1] rather than the family of hardware that could implement
such algorithms inexpensively.

[1]: https://en.wikipedia.org/wiki/Digital_differential_analyzer_(graphics_algorithm)

Sutherland also mentions the remarkable leapfrog-integration property
that is also true of Minsky's circle-drawing algorithm:

> Theory and simulation show that just as in the incremental equation
> used for generating circles (see Chapter V), the latest value of
> increment must be used if the curve is to close.  Therefore, the
> additions cannot all occur at once; the order shown in Figure E.2 by
> the numbers 1–4 next to the adders makes the circles and ellipses
> close.  In a serial device it is possible to do the four additions
> in just two add times by having only a one bit time delay between
> the two additions for each coordinate, i.e., (?+) just before (+).

And indeed his figure E.2 does present an RTL diagram for something
similar to Minsky's algorithm.  (For "serial device" read "bit-serial
adder".)  As I read his variant, it works as follows:

    xremainder += xincrement
    if carry:
        yincrement += ycurvature
        yscope++
    elif borrow:
        yincrement -= ycurvature
        yscope--

    yremainder += yincrement
    if carry:
        xincrement += xcurvature
        xscope++
    elif borrow:
        xincrement -= xcurvature
        xscope--

This looks like a lot of code but it's really just four arithmetic
operations within the feedback oscillator, two of which are
conditional, and two conditional operations driven by the oscillator.
(There's an extension to arbitrary conics.)

He says this sort of conditional addition/subtraction goes beyond "the
usual practice in incremental computers", which he says is just a
conditional increment or decrement (the ordinary sort of carry
propagation).  However, [Wikipedia seems to describe what he's doing
as "the basic DDA integrator"][3].

[3]: https://en.wikipedia.org/wiki/Digital_differential_analyzer#Theory

Sutherland points out that this configuration allows us to produce not
just circles and ellipses (as is usual for Minsky's algorithm), but
also straight lines (by setting the curvature numbers to 0) and
hyperbolas (if the curvature numbers are of the same sign).  I think
it also has a guarantee that it produces densely packed pixels,
without space between them, which is harder to achieve with Minsky's
algorithm.  (To guarantee that it doesn't dawdle on the same point, I
think you can prescale the increment values until one of them is
essentially 1, e.g., 0.1111111111 if you're using 10-bit fractions.)

If we again treat the xscope.xremainder and yscope.yremainder pairs as
fixed-point numbers with the "remainder" being the fractional part,
then we can see that this is not quite Minsky's algorithm, because it
has four registers instead of two (if we treat the curvatures as
constants), and, more surprisingly, it runs *backwards*!  The
frequency of carries (or borrows) gives us the *derivative* of the
variable being thus incremented.  So if in some interval of time X
increases (or decreases) past 12 integer values, then in that interval
of time Y's derivative will be increased (or decreased) by ycurvature
12 times.  So Y's second derivative is thus approximated by ycurvature
times X's first derivative, and *mutatis mutandis*.

This requires less hardware than the more straightforward approach of
X -= k × Y; Y += k × X because that requires a multiplier.  By
encoding the derivative in this way as a neuron-like spike train, we
can multiply by repeated addition instead.

(Spike-train circuitry is experiencing some renewed interest nowadays,
both to reduce power consumption and as delta-sigma circuits for
higher stochastic DSP speed — the multiplication of two random spike
trains is their AND, and their average can be obtained by a
multiplexer driven from a random bitstream.)

I think the commonplace circle midpoint algorithm can also be cast
into such a form, but triggering additions and subtractions from
comparisons rather than carries.  It would be interesting to see if
there's a stepwise transformation to show the equivalence — or
non-equivalence! — of the two circle-drawing algorithms.

Sutherland reports success at the time at building a bit-serial DDA
machine at the time using some 36-bit delay lines and some 20-MHz
logic chips, plotting a display point every 900 ns.
