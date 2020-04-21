Here's the plan based on my previous BubbleOS notes:

- import a snapshot of Yeso to get started on drawing something
    - prune stuff I won't use, like the Python binding
- bottom-up approach:
    - define minimal viable Veskeno virtual machine, maybe something a
      little bigger than Chifir, maybe including storage, interrupts,
      real-time clock, audio
    - write a minimal viable implementation of it in C using Yeso (128
      LoC)
    - define a minimal viable assembly language Abhar for it, maybe
      Forth-style
    - implement Abhar in itself (512 LoC)
    - implement a bootstrap interpreter for Abhar in, say, JS (128 LoC)
    - define minimal viable Leconscrip
    - implement a compiler for MV-Leconscrip in Abhar (2048 LoC)
    - (where does my vaunted pervasive observability come in?)
    - define a garbage-collected language for writing compilers in
    - write an *interpreter* for it in MV-Leconscrip (2048 LoC)
    - define a more full-featured Leconscrip
    - implement a compiler for the full-featured Leconscrip in the
      GCed language (2048 LoC)
    - write a text layout and rendering engine in Leconscrip (1024
      LoC)
    - write a parser generator in the GCed language (512 LoC)
      targeting Leconscrip
    - write a Markdown parser in it (128 LoC)
    - write a dependency management system (4096 LoC) for use in
      building and also updating the display
    - write a kernel (8192 LoC) including a filesystem

That approach looks like 128+128+128+512+512+1024+2048+2048+2048+4096
lines of code, 128+256+4096+8192 = 12672, before I can start seeing
things on the screen.  The majority of that is actually the kernel.
The COCOMO model (see file `estimation.md`) suggests that 12.672 KSLOC
should take about "34.5 person-months", or 9.1 person-months for me.
Moreover, the `2.5 * (personmonths**0.38)` schedule formula suggests
that, even splitting the work up among multiple programmers, it would
take 9.6 months.

So I really need to come up with (a) a better overall design that
doesn't need so much fucking code and (b) a more incremental path
which allows me to start running things a lot sooner.  I can probably
manage 512-1024 lines of code in a week; what could I do that would be
a meaningful piece of my goals for Derctuo within that scope?

The options above include writing a Veskeno simulator in C, writing an
Abhar interpreter in JS, and writing a Markdown parser in an
as-yet-unimplemented parser generator, or all three (128 LoC each);
writing an Abhar assembler in Abhar, writing a parser generator in the
GCed language, or possibly both (512 LoC each); or possibly writing a
text layout and rendering engine in Leconscrip.  But maybe I could do
something smaller.  Here are a few random ideas:

- Implement a simple high-level garbage-collected language in JS or
  LuaJIT.
- Implement a minimal text file viewer on top of Yeso (in whatever
  language) to which I can add hypertext, transclusion, formula
  evaluation, and smarter layout.  Monospace might be okay to start.
- Prototype the dependency system in Python, using C programs to
  execute the "build steps" instead of Leconscrip programs.
- Prototype some explorables in JS/DHTML with a view to getting a
  clearer idea what kinds of facilities are useful for them; maybe
  take a note from Dercuano and figure out how to make it as
  explorable as possible.
- Try plotting some calculations in JS/DHTML.
- Fiddle around at random on ObservableHQ.
- Reimplement Cant in JS.
- Write some games in a new programming environment.

It's not clear exactly what the top-down view looks like, either.  I'm
pretty sure I want something mostly web-browserish for the user
interface, but without so much cruft; maybe IMGUI will simplify
matters somewhat.
