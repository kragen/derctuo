I was thinking about the BASIC systems of my childhood and what a
similar system might look like now.  The overall emphasis was on
making them *easy*.

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
would eliminate any need for running user-defined cleanup code.)

So, thinking about BASIC and Logo, I wondered what a modern sort of
BASIC would look like, one that was as easy as real BASIC in the ways
that BASIC was easy, but easier in the ways that BASIC was hard.  And
even though I've said above that a lot of the important aspects were
the IDE — you were always running the program "in the debugger", you
could stop and look at variables and fix it and CONTinue, etc. — I'm
going to focus on the purely linguistic aspects here.

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
