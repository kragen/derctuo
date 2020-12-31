I was thinking about the BASIC systems of my childhood and what a
similar system, tentatively called Scribal Basic,
might look like now.  The overall emphasis was on
making them *easy*.

What BASIC was like
-------------------

One key aspect was the "IDE": you could interrupt the program at any
time, type bits of code, inspect some variables (the "PRINT" command
had a shortcut spelling "?" for this purpose, so you could type "?X3"
to see the value of X3, a feature important enough that FORTH spent
the single-character word "?" on it as well), modify the program, and
continue from where you left off.  So in a sense you were always
running "in the debugger", just as with Smalltalk, Lisp, and FORTH.

Microsoft BASIC-80 had a set of vi-like line editing commands which I
couldn't understand, because I couldn't see what was going on, so I
would modify the program by retyping lines of code from scratch, with
the line number.  The 8086 version of Microsoft BASIC improved this
because it had arrow keys and modeless screen editing, so you could
use the arrow keys to move up to a previous output line (from LIST,
say), modify it, and hit Enter, which worked like retyping the line.
Because you could replace, say, line 30 of the program to say "INPUT
X" by typing `30 INPUT X`, this had the effect of changing the program
in memory to say what you'd edited that line to say, as if you were
WYSIWYG-editing it through a tiny one-line-long window.

Another aspect of BASIC's easiness was that it had no mutable data
structures other than variables.  It had array variables, but no way
to alias them.  There were no records, no containment, no foo.bar.baz,
no pointers (other than indices you could potentially use to index
arrays).  Strings were immutable.  This seems to have been important
to usability; Joel Spolsky (?) reports that the `with` statement,
which in VBA (as in Pascal or JS) imports the contents of a record
into the variable namespace, was a significant boon to VBA's
usability.  (I can't find Joel's post now.)

You also didn't have to deal with data types, and I didn't, except for
the distinction between strings and numbers, a distinction which Perl
and JS eliminate.  More advanced BASIC programmers knew that by
defining some or most of their variables as integers they could speed
up their programs by one or two orders of magnitude, because of course
all the floating-point math was done in software, and by default when
you wrote `FOR I = 1 TO 10` you were implicitly doing a floating-point
addition and comparison every time through the loop.

There was no scoping; all variables were global, declaration was
implicit, and as in Perl, initialization was automatic — unassigned
variables had the value 0, or "" if they were strings.  Like the lack
of types, this was super bug-prone, but it was also simple — you never
lost a variable value because it went out of scope, or had two
different variables with the same name, or had code that worked at one
point but then stopped working due to an unrelated change giving an
uninitialized variable a nonsense value.

BASIC-80 had a one-level parser that didn't need whitespace to
recognize keywords, but variable names were limited to only two
characters.  The consequence was that you could write things like
`ifx=ythen300elseprintx` and have it parse.  This was terrible for
readability but probably also improved the language's "easiness" by
making it more forgiving: you couldn't break your program by leaving
out whitespace.  Relatedly, if you wanted to PRINT multiple things on
the same line, you were supposed to separate them with `;`, but
BASIC-80 (and I think even GW-BASIC) didn't enforce that.

There was a strong distinction between the user program and the
system, and the temptation was to think that "learning to program"
meant memorizing the capabilities the language provided.  BASIC's
extensibiilty was almost zilch.  GOSUB was the limit; if you defined a
subroutine to draw a line, for example, you might invoke it as
follows:

    1450 X1=37:Y1=102:X2=128:Y2=17:GOSUB 3000

Compare that to [the LINE statement][0] added to Microsoft BASIC at
some point (it was present on at least the versions for the TRS-80
Color Computer, the IBM PC Jr, and the Zenith Z-100):

    1450 LINE (37,102)-(128,17)

[0]: http://www.antonis.de/qbebooks/gwbasman/line.html

From the perpective of a 7-year-old, which I was when I started
programming the CoCo, that's a huge difference.  I learned to define
subroutines in Logo when I was 5 or 6; I didn't figure out how to
transfer that knowledge to BASIC until after learning Pascal (at 11),
or maybe even later.

The other problem created by the strong system-code/user-code
distinction was that, even if you did implement the code to draw a
line in high-level BASIC, it would be so slow that you could see it
drawing each individual pixel, even on the IBM PC Jr, which ran at
almost 1 MIPS but only [about 0.2 Dhrystone MIPS][1].  So BASIC was
too slow to be used as other than a "scripting language" in that
sense.

[1]: http://www.netlib.org/performance/html/dhrystone.data.col1.html

However, the great *benefit* provided by the system-code/user-code
distinction was that your BASIC program couldn't corrupt the system's
structures, at least until you pierced the veil with PEEK and POKE.
When you interrupted your program — a common thing to do to fix bugs
you'd just noticed, or to recover from an infinite loop — BASIC would
tell you you were at some line number or other, not in the middle of
some internal Bresenham-point-calculation routine invoked by LINE.  On
one hand, this did not facilitate studying and extending the system,
but on the other hand, it meant that the behavior you saw was always
explicable in terms of your program — you didn't *have* to study and
extend the underlying system to debug your program.

(Vaguely related thought: transactions simplify debugging and error
recovery, at least as long as they don't make debugging impossible.
What if every subroutine call were a nested transaction?  It would
simplify exception handling, too, since rolling back the transaction
would eliminate any need for running user-defined cleanup code.  See
[transaction-per-call.md].)

So, thinking about BASIC and Logo, I wondered what a modern sort of
BASIC would look like, one that was as easy as real BASIC in the ways
that BASIC was easy, but easier in the ways that BASIC was hard.  And
even though I've said above that a lot of the important aspects were
the IDE — you were always running the program "in the debugger", you
could stop and look at variables and fix it and CONTinue, etc. — I'm
going to focus on the purely linguistic aspects here.

Syntax and control flow
-----------------------

From my experience with Logo, I don't think the absence of real
subroutines with parameters was really a key thing that made BASIC
easier.  In fact, I think Logo might have made things *easier* by
having named subroutines with parameters.

But if you don't have records or other heterogeneous aggregate data
types (tuples or whatever) you need some way for a subroutine to
return multiple atomic units of data.  In BASIC this was easy, because
all the variables were global, so you could just change them.  Octave
(another champion at making programming accessible to nonprogrammers)
fixes this by explicitly listing named return values in the procedure
header.  A different approach is to make parameters implicitly inout,
for example by always using pass-by-reference.

Another way to compensate for not having records is by using closures,
but closures can be confusing.  But Smalltalk-like "blocks" don't have
to be confusing; consider Logo's REPEAT 4 [FD :SIDE RT 90] — it's
apparently easy enough for kids to use.

So I propose that Scribal Basic should support CLU-like or Ruby-like
block arguments, so you can define `repeat` in the language itself,
although learners should be able to lock it so they don't end up in
the middle of it when they interrupt the program:

    [repeat n]
    for i = 1 to n:
        yield

I think Python's indentation-based syntax with colons has been shown
to be easier for learners to understand, but if not, you could spell
this as follows:

    [repeat n]
    for i = 1 to n
    yield
    next i

You would invoke this with something like the following:

    repeat 4:
        fd side
        rt 90

Or, if the indentation goes away:

    repeat 4 {
        fd side
        rt 90
    }

The block argument is invoked with `yield`, and because it's not a
first-class object, it becomes a downward funarg; it can't be captured
and stored for later use, so resources can be reclaimed with stack
discipline.  This does require a little bit of trickiness with the
calling convention: the block is running in the lexical context where
it was defined, with access to variables like `side`, but the
activation record of `repeat` is still active, so `fd` and `rt` get
their activation records pushed on top of `repeat`'s.  And when we
reach the end of the block we must "return" into the body of `repeat`,
before it returns to the caller of `repeat` which contained the block.

Because all the parameters are passed by reference, you can pass
variables to return values in as parameters, especially useful for
IMGUI kinds of things.  Because these references are also not
first-class values, they also cannot be saved for later, so they do
not impede stack discipline for resource management.

Nonlocal exits from block arguments are potentially tricky.  If you
implement GOTO and RETURN in the straightforward way, three potential
problems come up:

1. You could RETURN from inside a block without giving `repeat` or
   similar things a chance to clean up their activation records.  They
   won't leak memory in the usual stack implementation — you just
   restore the stack pointer to what it was on entry to the subroutine
   you're returning from — but you could imagine wanting, for example,
   to unlock a lock or restore some graphics context state.  So
   whatever cleanup code is necessary would need to be activated.

2. If you can GOTO from within the block to outside the block, not
   only could you circumvent such cleanups, you could enter the block
   repeatedly, which means that there would be more than one
   activation record on the stack of `repeat` or a similar procedure
   for the block to potentially return into if it ever manages to
   successfully terminate.

3. If you can GOTO from outside the block to within it, you can end up
   at the end of the block without anyplace to return to.

The simplest way to avoid all these problems is to eliminate GOTO and
RETURN from the language.

Blocks that take parameters could be spelled in a few different ways.
At one point I was thinking to use [] (otherwise unused in BASIC) for
parameters in general, so maybe you could spell a block that takes X
and Y parameters as [X, Y] { ... }, for example.  Or you could put
them after the colon if you use Python-style indented blocks with
colons.

But really you don't need parameters for blocks if parameters are
passed by reference.  You can just use out-parameters for the
higher-order subroutine you're invoking:

    eachpoint x y:
       print "x: " x "y: " y

Here `x` and `y` are variables in scope in the caller of `eachpoint`
(perhaps local, perhaps global, perhaps parameters) which `eachpoint`
can assign to before invoking `yield`.

In PostScript, we define paths by a series of commands: moveto,
lineto, closepath, arc, and so on.  We can then stroke, fill, or clip
to these paths, and IIRC in Display PostScript we could also handle
events in those paths.  This sort of thing seems like a good use of
blocks for passing complex data down the stack.  As a simple example,
we could define a subroutine that both strokes and fills a path:

    [strokefill]
    yield              ' stroking is default behavior for discoverability

    fill:
        yield

And to handle a mouse click event inside a path, perhaps we could
store the coordinates and a bitmask of buttons via out parameters:

    [handleclick x y buttons]
    getmouse x y buttons         ' get mouse state from intrinsic

    checkwithinpath within x y:  ' standard routine for containment check
        yield                    ' delegating path definition to block passed in

    if not within:
        buttons = 0            ' zero out the buttons so caller knows to ignore

I'm not sure if a single block argument will be adequate, because you
probably want to define things like "if this button is being clicked,
do such and so", and that is difficult to express with a single block
if both the button geometry and its action are represented with
blocks.  You could do something grody like this:

    button bletches:
        if bletches = "draw":
            drawmybutton
        else:            ' it got clicked, since that's the other possibiity
            print "mybutton clicked"

But some kind of syntax to pass multiple block arguments, or name
them, might be better.

Other cases where you might want more than one block argument include
clipping (an operation that takes a clipping path and a
drawing — though this is handled in PostScript by having the `clip`
operator change the current clipping path until the next `grestore`);
looping constructs with a "final" block; exception handling with a
handler block; union and difference of paths; and 3-D CSG.

Naming all the block arguments might be a good idea:

    [handleclick x y buttons &path]
    getmouse x y buttons

    checkwithinpath within x y {
        path
    }

    if not within {
        buttons = 0
    }

This permits the more straightforward formulation:

    [handleclick x y buttons &path &handler]
    getmouse x y buttons

    checkwithinpath within x y {
        path
    }

    if within {
        handler        ' invoke second block argument
    }

This might be invoked simply with two juxtaposed blocks:

    handleclick a b c {
        moveto 100 100
        rlineto 0 200
        rlineto -100 0
    } {
        print "triangle clicked" a "," b "buttons" c
    }

In fact, it might be written with them:

    [handleclick x y buttons &path &handler]
    getmouse x y buttons

    ifwithin x y {
        path
    } {                       ' second block argument to ifwithin is handler
        handler
    }

But if we're using the Python-like indented-block syntax, you need
some kind of keyword to introduce the block:

    [onclick x y buttons &path then: &handler]
    getmouse x y buttons

    ifwithin x y:
        path
    then:  ' second block argument to ifwithin is also introduced with "then:"
        handler

### Argument separation ###

Logo and Tcl come down firmly on the side of juxtaposed arguments, but
BASIC traditionally separates arguments with commas, for user-defined
functions like DEF FNA, for built-in functions like INT and RND, and
for some commands like SAVE "FOO",A.  Other commands separate
arguments with semicolons (INPUT "Name";N$) or a combination (PRINT
I;"th month", M(I)).  PV-WAVE IDL goes further and separates even the
function name from the first argument with a comma: F,X,Y.

The argument in favor of separating arguments with commas is
redundancy for error reporting; if `rlineto 0 -100` gets interpreted
as `rlineto (0-100)` it could be difficult to figure out why you were
getting an error or an incorrect effect, or that you need to write
`rlineto 0 (-100)` instead.

Smalltalk uses keywords to separate arguments: ary at: pos put:
element, which would read a little better as ary at=pos put=element or
ary at: pos, put: element.  And I'm already considering that maybe
Scribal Basic should use such keywords to introduce block arguments,
at least after the first one.

For the time being I'm sticking to simple Logo-like juxtaposition of
arguments despite infix syntax, but I'm noting this as a potential
trouble point.

Data model
----------

I think the unification of strings with numbers as done in Perl, the
Bourne shell, and Tcl is a significant improvement in usability,
especially for novices, and worth keeping, although it undermines
Scribal Basic's claim to be a Basic.  Awk and JS also try to do this,
but they do it by guessing when something is supposed to be a number
and when it's not, and there are some operations that handle the two
differently, notably comparisons and, in JS, `+`.  I think this is a
mistake.

I also think built-in hash tables (as in awk, Perl, Tcl, Lua, and JS)
improve usability a lot, even without being first-class values, as
they aren't in Tcl, Perl 4, and awk.

The possibility of passing a nonexistent hash table entry as an
argument by reference as an argument suggests a lurking danger of
autovivification.  This can be ameliorated by not making hash tables
first-class values, so the question of what to do when reading
a("foo")("bar") doesn't arise, and by adopting the Lua convention for
existence: a nonexistent hash-table entry is indistinguishable from
one to which nil has been assigned.  This is bug-prone but probably
better than the alternatives in this context.

This way, if someone says `foo bar["baz"]` and bar doesn't have "baz"
in it yet, we can safely insert a nil at "baz" into the hash table
`bar` before invoking `foo` with a pointer to that nil, which it can
then set to something else if it wants.  However, this pretty picture
is complicated by the possibility of needing to rehash the table to
expand it before `foo` attempts to mutate it.

This can be avoided, rustily, if it's impossible to insert anything
else into `bar` in the meantime, for example because no other
reference to `bar` is passed to `foo` or used by a block argument of
`foo`.  Alternatively, we could pass in a writing thunk rather than a
raw memory address, or apply a lock to `bar` to prevent insertion
until `foo` returns, a lock the insertion routine would have to
respect.

If we're going to write subroutines that process arrays of numerical
data, we need some way to pass the whole array as an argument.
Traditionally in BASIC this is done, inefficiently, by sharing a
global array, while in Algol 60 it was done with call-by-name,
allowing constructs analogous to the following:

    [sum i n item total]
    total = 0
    for i = 1 to n:
        total = total + item

which could be invoked as, for example, `sum i 10 a(i)*b(i) dp` to put
a dot product into `dp`.

I think call-by-name is a terrible idea, though I'm not clear that
it's really that much worse than implicit call-by-reference.  But the
alternative to call-by-name is to pass entire arrays by reference,
which seems like the right choice:

    [dotproduct n p q total]
    total = 0
    for i = 1 to n:
        total = total + p(i) * q(i)

Much of the language design is aimed at pretty high and
highly-predictable efficiency with a simple compiler: stack discipline
for variable storage, limited pointers, no records, and so on.  But it
seems that if you're going to make the language stringly-typed like
Tcl, you're going to have to do some type inference to figure out
which variables are really numbers.  This is going to be complicated
by pervasive mutability and call-by-reference, since potentially any
function you call with a variable could change the type of that
variable.  And any time you invoke `yield` (or a named block argument)
that block can potentially mutate any global variable or by-reference
argument, including changing its type.

But I think in most cases you can infer at least numeric or string
nature for variables, and in, out, or inout mode for parameters.  Most
subroutines won't take block arguments; most parameters won't be
modified; etc.

Scoping
-------

Nested scopes are probably a mistake for novice usability, and
probably implicit global is the wrong choice — it would render
disastrous the simple `repeat` definition above, which mutates `i`
without declaring it local — especially given the possibility to pass
things by reference.  The usual annoying issues of producing closures
in a loop (do they all alias the same underlying variable?) disappear
with downward funargs.

But mutable global variables probably are needed.  Ruby's solution of
prefixing them with `$` seems like the best tradeoff, avoiding the
"action at a distance" effect of explicit declarations.

Imaging model
-------------

Making graphics was one of the most important aspects of both Logo and
BASIC.  Even on the H89 I was making ASCII-art graphics.  Nearly all I
did in Logo was make graphics, a fact which will surprise anyone who's
seen my adult drawings.  Other people I've talked to about their
childhood Logo experience also talked about making graphics.  The
graphics capability of IBM PC BASIC, Z-BASIC, and GW-BASIC was what I
spent all my time on when I programmed those machines.  Proce55ing is
popular with novice programmers today and mostly focused on making
graphics.  And James Hague [describes his experience learning to
program][2] as follows:

> ...I can talk about the Atari 800 I learned to program on.
> 
> Most games didn't use memory-intensive bitmaps, but a gridded
> character mode. The graphics processor converted each byte to a
> character glyph as the display was scanned out. By default these
> glyphs looked like ASCII characters, but you could change them to
> whatever you wanted, so the display could be mazes or platforms or a
> landscape, and with multiple colors per character, too. Modify one
> of the character definitions and all the references to it would be
> drawn differently next frame, no CPU work involved.
> 
> Each row of characters could be pixel-shifted horizontally or
> vertically via two memory-mapped hardware registers, so you could
> smoothly scroll through levels without moving any data.
> 
> Sprites, which were admittedly only a single color each, were merged
> with the tiled background as the video chip scanned out the
> frame. Nothing was ever drawn to a buffer, so nothing needed to be
> erased. The compositing happened as the image was sent to the
> monitor. A sprite could be moved by poking values in position
> registers.
> 
> The on-the-fly compositing also checked for overlap between sprites
> and background pixels, setting bits to indicate collisions. There
> was no need for even simple rectangle intersection tests in code,
> given pixel-perfect collision detection at the video processing
> level.
> 
> What I never realized when working with all of these wonderful
> capabilities, was that to a large extent I was merely scripting the
> hardware. The one sound and two video processors were doing the
> heavy lifting: flashing colors, drawing characters, positioning
> sprites, and reporting collisions. It was more than visuals and
> audio; I didn't even think about where random numbers came
> from. Well, that's not true: I know they came from reading memory
> location 53770 (it was a pseudo-random number generator that updated
> every cycle).
> 
> When I moved to newer systems I found I wasn't nearly the hotshot
> game coder I thought I was. I had taken for granted all the work
> that the dedicated hardware handled, allowing me to experiment with
> game design ideas.

[2]: https://prog21.dadgum.com/173.html

A common observation of kids (and, to a lesser extent, novice adult
programmers) is that they quickly pick up how to use the built-in
facilities of the environment, but struggle to build their own
abstractions for hierarchical composition, even when they aren't using
generalization-impaired environments like BASIC-80.  Later in the
article quoted above, Hague describes his own process of learning to
do this when thrust into "the cold expanse of real programming".

So it's really important that you can do [this kind of thing in a
trivial amount of code in GW-BASIC][3]:

    10 screen 2:for i=0 to 20:line(i*31,0)-(0,i*9):next

[3]: https://hwiegman.home.xs4all.nl/gw-man/SCREENS.html

This generates a graceful string-art approximation of a quadratic
Bézier curve when you RUN it, which is super cool when you're 8.  It's
only three bytes longer than a minimal Java program that does nothing
at all:

    class X{public static void main(String[]args){}}

Further contrast this "hello, world" program with Swing, and consider
the level of novice-accessibility it demonstrates:

    import javax.swing.SwingUtilities;
    import javax.swing.JFrame;
    import javax.swing.JLabel;

    public class HelloWorldSwing {
        public static void main(String[] args) {
            javax.swing.SwingUtilities.invokeLater(new Runnable() {
                    public void run() {
                        JFrame frame = new JFrame("HelloWorldSwing");
                        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                        frame.getContentPane().add(new JLabel("hello, world"));
                        frame.pack();
                        frame.setVisible(true);
                    }
                });                        
        }
    }

That's over an order of magnitude worse than the BASIC line-art
program.  I don't think you can get it much more compact than that
with Swing, and that's appalling.

So I think Scribal Basic, despite the name, would need to make it easy
to draw graphics.  But I think most people will quickly get frustrated
with the limits of 1980s-style opaque line-art polygons in 4 or 16
flat colors, or even 24-bit flat colors, even for cartoons.  So I
don't think GW-BASIC-style flood-fill and XORing into the framebuffer
is really going to get us very far.

I think instead something like the PostScript/PDF/SVG imaging model is
better, at least for 2-D graphics, where you build up two-dimensional
paths one at a time and apply stroke/fill/eofill/clip operations to
them, with alpha-blending and gradients.  (The GLSL fragment shader
approach is more powerful but more challenging.)  There are a lot of
ways for people to build graphics for that model: interactive drawing
programs, making JPEGs or PNGs or H.264 frames that are then loaded
in, rendering from 3-D models, constraint solvers like SKETCHPAD and
SolidWorks, and so on.

But I'm going to focus on the most straightforwardly usable imperative
programming approach to defining paths, which I think is either turtle
graphics or moveto/lineto with numerical coordinates.  It's fairly
straightforward to define one in terms of the other; here's the code
for turtle graphics in PostScript I used for Heckballs:

    % Turtle graphics.

    /seth { /theta exch def } def
    /rt { theta add seth } def
    /lt { neg rt } def
    /pd { /turtle-pen {rlineto} def } def
    /pu { /turtle-pen {rmoveto} def } def
    /here { [ /turtle-pen load  theta  currentpoint ] } def
    /return { aload pop  moveto  seth  /turtle-pen exch def } def
    /fd { dup  theta sin mul  exch theta cos mul  turtle-pen } def
    pd  0 seth

But of course the target audience for Scribal Basic is people who
can't figure out how to write such an adaptor layer, so you'd want it
to be part of the base system and one that they don't accidentally get
lost in.  In Scribal Basic as described so far, it would be
something like this:

    [seth θ] ' set heading
    $θ = θ   ' $ to indicate global

    [rt θ]       ' turn right
    seth $θ + θ * π / 180

    [lt θ]       ' turn left
    rt -θ

    [pd]         ' put turtle's pen down
    $pendown = 1 ' can't use higher-order functions like PostScript

    [pu]         ' pen up
    $pendown = 0

    [fd n]           ' go forward n paces
    Δx = n * sin($θ)
    Δy = n * cos($θ)
    if $pendown:
        rlineto Δx Δy
    else:
        rmoveto Δx Δy

    [saveturtle pen θ x y] ' can't return a heterogeneous array like PostScript
    pen = $pendown
    θ = $θ
    currentpoint x y   ' currentpoint subroutine from primitive model sets x, y

    [restoreturtle pen θ x y]
    $pendown = pen
    seth θ
    moveto x y

    [saveexcursion]       ' do some turtle-drawing in a saveexcursion:
    saveturtle pen θ x y  ' to return to where you started when you're done
    yield
    restoreturtle pen θ x y

Though this is not as graceful as the PostScript, and a lot longer, I
think it's actually easier to read.

The initialization code maybe needs to get invoked somehow, although
by using a `$penup` instead of `$pendown` we could get the right
defaults from default initialization to zero.

The higher-order function `saveexcursion` suggests using the block
facility as a substitute for PostScript's gsave/grestore, which save
the current stroke width, color, point, path, and so on, and then
restore them.  And, as mentioned above, this would also work for
`fill`:

    fillcolor 128 31 45
    fill:
        moveto 100 100
        fd 50
        rt 100
        fd 40

Aside from the possibility of providing a fourth α argument to things
like `fillcolor`, you could *also* use the block facility to do a
drawing with a global α.  Also, you could use the same approach to
provide double-buffering, scaling or rotation or skewing or
perspective distortion, drawing on an offscreen canvas that you later
composite in, and so on.

Sound
-----

Similarly, sound was always a big deal for motivating programming, but
you can do a lot more sound now on a computer than you could in 1985.
In the article of James Hague's I mentioned above, he was setting two
registers on his Atari 800 to produce a musical tone, but now a cheap
soundcard can produce literally any sound a human can hear, if you
have a precomputed CD-DA recording of it.

There are existing DSLs for computer music, such as CSound,
SuperCollider, ChucK, Sporth, [Faust][4], and Pure Data.  Unfortunately I don't
have enough experience with them to venture an opinion as to what
subset of their capabilities could be reasonably replaced by a Scribal
Basic embedded DSL.  Some of them, like Sporth, represent sounds as
bits of code that execute (conceptually at least) on every sample;
this is not harmonious with the way I've conceptualized Scribal Basic,
at least so far, although you could do it if you added some kind of
threading.  Or you could set a global function as the "sound
generator", which would be invoked to generate sound samples from some
kind of event loop.

[4]: https://ccrma.stanford.edu/~jos/faust/

At a minimum, I'd think you need support for playing WAV and ogg files
(with a built-in mixer), playing MIDI files (with a built-in
soundfont), and playing sequences of pitches and durations computed by
the program (using the same path as MIDI).  But it would be super cool
to be able to do subtractive synthesis, additive synthesis, FM
synthesis, distortion, flanging, pitch bending, ADSR envelopes,
portamento, tremolo, vibrato, reverb, digital waveguide synthesis, and
custom wavetables (from samples or otherwise).
