Here’s an interesting idea for how to do Derctuo: a giant WYSIWYG
document whose source format is a plain text file including data,
code, text, and formatting in a single document, potentially of 128
mebibytes or more; but with computational output rigidly segregated to
a cache management system.

Precedents
----------

### Danny O’Brien’s Life Hacks ###

The immediate inspiration for this is Danny O’Brien’s “Life Hacks”
ethnographic research finding the widespread use of One Huge Text
File.  He found that many of his interviewees maintained all their
notes in a single humongous text file, which they navigated by text
search.  On a modern computer, Emacs incremental-search is capable of
searching through hundreds of megabytes per second, so it’s rare to
even need any indexing.

### Volks-Hypertext ###

Eric Raymond’s “Volks-Hypertext” browser for the Jargon File
demonstrated how to improvise a fairly instantaneous hypertext system
atop a large text file: the text file was rendered in more or less the
usual way, but keywords in curly braces like “{grok}” were treated as
links to a line beginning with “:grok:”, and the file was preprocessed
to generate an index of all such lines after the fashion of ctags,
with byte offsets stored.  Searching the index file and jumping to a
given byte offset was reliably fast, even in MS-DOS on a 386.

### askSam ###

The cult semistructured database askSam has barely more structure: an
askSam file is a collection of records, which are just free text
strings up to a few kilobytes in size, with fields defined by
searching for the field name followed by square brackets — everything
on top of that is added by the askSam query language.  A full-text
index makes relatively powerful queries acceptably fast.

### Alph and Halp ###

Darius Bacon’s Alph (A literate programming hack)
and Halp systems automatically re-evaluate all the
specially-marked code in a document upon demand, placing the resuls of
each snippet after the snippet itself.

### Org-mode ###

Org-mode adds a little bit of lubrication to text-file viewing: the
Emacs outline-mode ability to collapse and expand sections of the
file, but with more pleasant keybindings.  And it also has magic
syntax for inserting hyperlinks: [[http://example.com/][example URL]]
displays just as an underlined “example URL”, but links to the given
URL, and it also supports links to places within the file.  Org-mode’s
“src blocks” offer the possibility to
[display textual or graphical output inline in the editing buffer](https://orgmode.org/manual/Results-of-Evaluation.html#Results-of-Evaluation).

### Cassowary and TeX ###

Cassowary is a constraint-based layout system that offers perhaps a
bit less power than CSS, but has extremely efficient algorithms to
execute it.  (I haven’t actually tried it.)  TeX, too, has extremely
efficient layout algorithms which also produce somewhat nicer results
than CSS.

### WordPerfect Reveal Codes ###

Before Microsoft Windows, WordPerfect was the most popular word
processing software, and its users’ favorite feature was a thing
called “Reveal Codes”, which split the screen into one half with the
WYSIWYGish text you were editing at the top and a complete
representation of the word processor’s underlying representation at
the bottom, with formatting markup displayed in between bits of text.
This made it easy to see why your document was formatting incorrectly
and fix it.

### Lotus 1-2-3 ###

Lotus 1-2-3 displays the tabular output of a program written by the
user by defining formulas in cells.  It analyzes the dependencies
between the cells to discover as safe dependency order to recalculate
them in when there is a change, and it only displays the source code
of the cell you are editing at a given moment.  It imitated VisiCalc,
the “killer app”, but its dependency-order recalculation was new.

### Jupyter notebooks ###

Jupyter’s “notebook interface” is an enhanced REPL which permits the
inline display of graphics, text formatted with LaTeX or HTML, etc.,
as results of the REPL commands (“cells”).  It is accessible via HTTP
or HTTPS, allowing people to share code easily.  Also, it stores the
output in the same text file as the code in the cells, even when it is
graphical or irreproducible.

Jupyter has become the standard
interface to programming for an enormous number of people nowadays.
But it has some serious drawbacks: the output displayed may not be up
to date with the code in the file, re-evaluating the whole file may
not be safe (it’s common for people to put utility scripts in
notebooks that do things like wipe a database), the output being
interpolated into the source code makes the notebook files bulky and
difficult to version-control with systems like Git, it’s awkward to
reuse code, and normally you have to start out the notebook with a
bunch of preliminary noise like module imports.

### Explorable explanations ###

“Explorable explanations” are, mostly, web pages containing
interactive visualizations of algorithms; the best ones I’ve seen are
Amit Patel’s, for example his [visualization of A* pathfinding][0] or
of [generating terrain with Perlin noise][1].  Mike Bostock, the
author of d3.js, has written [many excellent explorable explanations
as well][2].  The objective is to explain how a given algorithm works
by means of exhibiting its internal functioning on example data.  Bret
Victor has explored much of this territory as well, for example with
his visualization of Nile, and articulated guiding principles for the
field: that people engaging in creativity should be able to get
instant feedback on the implications and results of their ideas.

[0]: https://www.redblobgames.com/pathfinding/a-star/introduction.html
[1]: https://www.redblobgames.com/maps/terrain-from-noise/
[2]: https://bl.ocks.org/mbostock

### ObservableHQ ###

ObservableHQ is Mike Bostock’s exploration
of how the notebook interface could be
improved.  It uses a slight extension of JS as its language, its cells
each define a single value, and like Lotus 1-2-3, they are evaluated
in dependency order.

### R-Markdown and R Notebooks ###

Yihui Xie’s R-Markdown is a system (included in the free-software R
Studio, but also invocable from the command line) which extends
Markdown with embedded chunks of code in the R statistical programming
language and textual and graphical output produced by that code; the
code is optionally not visible in the output (echo=FALSE).  By
default, this “knitting” of the source R-Markdown document into a PDF
or HTML output with the graphics is a batch process, but for some time
R Studio has also had the option to evaluate these embedded code
blocks interactively with control-shift-enter, sending its output to
the R Studio console pane.  Because the chunks are normally run in
order, it is up to the author to track the dependencies between them
and topologically sort them in the file and to re-execute dependent
chunks when changing a thing they depend on.

However, recent version of R Studio have added an “R Notebook” mode
which displays the outputs of code blocks inline in an R-Markdown
document (whether textual or graphical), instead of in a separate
pane.  Rerunning the code and thus updating these outputs after
changing the code continues to require an explicit run-current-chunk
command, so the author is still responsible for keeping track of the
dependencies.

Unlike Jupyter, R Studio stores the output from the embedded code in a
separate file: an “R notebook” named foo.Rmd will have an accompanying
foo.nb.html which includes the text and graphics generated from it,
while foo.Rmd itself contains only the human-authored source code.
Xie’s explicit ambition is to improve the reproducibility of
computational research.

### `make` and other build systems ###

Stu Feldman’s `make` program, included with the UNIX operating system
for the PDP-11, is directed at accelerating the feedback programmers
need to improve their programs: by caching the results of compiling
parts of the program, automatically determining which parts of the
program have been edited since they were last compiled, `make` can
greatly accelerate the process of rebuilding the program after a small
change.  It does this in an almost wholly compiler-agnostic fashion:
like ObservableHQ, it only knows how to produce each of the
intermediate results in the build process by invoking some opaque
code, and what the inputs to that code are.  `make` does this at the
granularity of files and batch program invocations, while ObservableHQ
does it at the granularity of variables and snippets of code, but
modern software like Lucet can reduce the overhead of starting and
stopping a program to under 100μs, while modern software like
FlatBuffers or HDF can reduce the overhead of a program consulting
serialized input data structures to a minimum.

A limitation of `make` is that its knowledge of dependencies is not
reliable --- it relies on the programmer to describe the dependencies
in a “Makefile”, but usually the Makefile fails to capture the full
dependency graph.  For example, it is common for `make` to be unaware
that an object-code file depends on header files within a project
describing the ABI of other object-code files, a case for which
various “makedepend” systems have been devised; also, though, the
object-code files depend on system header files external to the
project and on the version of the compiler used, in the sense that
different object code would be emitted if the compiler or system
header files had been a different version.  The fallback response to
all of these problems is `make clean`, a conventional phony build
target whose “build rule” deletes all the files created by the whole
build process so that a subsequent execution of `make` will regenerate
everything from the virgin source code.

Other build systems, such as Apollo DSEE, its imitation Vesta, their
imitation ClearCase, Nix/Guix, Gitlab-CI, Urbit, and the popular
Docker, instead run the build steps in an environment more or less
isolated from anything that isn’t explicitly provided to that build
step as an input.  Because of the limitations of determinism in
conventional computing systems, these systems do still sometimes fail
to deliver full bitwise reproducibility, but they do aspire to it,
except possibly for Gitlab-CI.

### SPARK ###

Apache SPARK XXX

### ActivePapers ###

Konrad Hinsen’s ActivePapers research effort XXX

### The Java Virtual Machine ###

The JVM’s WORA aspirations XXX

### Geometer’s Sketchpad, KSEG, and GeoGebra ###

### Falstad’s Circuit.js ###

Design
------

So suppose we have a thing that is “really” just a huge text file, but
formatted in a WYSIWYG format like a book, and structured
hierarchically into sections and subsections in an org-mode-like way.
It uses a layout algorithm with good efficiency and adequate power.
You can include snippets of code into the file, easily toggling
whether the WYSIWYG view displays the code, its output, or both;
output can even be easily interpolated into the middle of a paragraph,
with a construct something like ${foo}.  The code can easily run
various kinds of ad-hoc queries on the file’s own contents.  Bits of
code defined in one section of the file can be invoked from other
sections, although a hierarchical namespacing mechanism limits
visibility and makes it easy to track dependencies.  It’s easy to
define data tables and add computed columns to them, and use the data
in those columns in other computations.  The file can define user
interfaces for things like drawing geometrical
compass-and-straightedge constructions, RPN calculations, or schematic
capture, and the data thus created becomes part of the text file — and
then it can be used as input to other code.

The output of code is strictly segregated from the “source” text file,
which contains only things the author explicitly chose to put into it,
but the code is deterministic and the outputs are cached in a file off
to the side so that they can be redisplayed without recalculating
them.

You can toggle between a “source” view, which shows the full contents
of the file, and the WYSIWYG view, or have both displayed at once.

The idea is that it should scale to 8 mebibytes or more of text
written by a single author and perhaps 128 mebibytes of other data
imported into the file from elsewhere: a personal memex, but taking
advantage of the computer’s power to augment human intellect through
more than just copying and retrieval of information.  A smooth path
allows ideas to gradually be solidified and explored: from
back-of-the-envelope calculations through sketches and simple
simulations through to refactoring into reusable parameterized models.

A crucial question for navigation is how interactive searching of
outputs works.  If you stick to searching only the source-code form of
the file, searching can be very fast, but in many cases you will be
missing the most interesting data.  On the other hand, that data can
be immense and full of things that are essentially random noise.

Interactivity and persistence
-----------------------------

Above I said that computational output is rigidly segregated to a
cache management system — the code within the document cannot mutate
the document.  Only the user can do that.  How can this be reconciled
with the need to add sketches, photographs, geometrical constructions,
circuits, DAGs, cellular automaton configurations, and the like?
Surely the user cannot always be expected to type in text from which
they can be computed!

Ephemeral explorable explanations like
[Amit Patel’s A* examples mentioned earlier][0] pose no problem for
this model at all.  A code chunk can evaluate to a function from (x,
y) pairs to (r, g, b) colors, for example, to produce an infinitely
zoomable, pannable image; that function (call it a “paint method”) can
run in an environment where it has no authority to access any state
other than (x, y) coordinates of requested pixels or to mutate
anything outside of its own local state.  Mouse coordinates and time
can be provided to a paint method in a similarly stateless fashion, as
they are on Shadertoy.

### Fragments ###

The movable blob position requires at least *some* state to persist
from one call to the next; this can be handled by an object consisting
of a pair of pure functions: one that maps a (current state, user
input event) pair to a new state (call this the “react method”), and
another that maps the current state to an image or animation (the
paint method from before).

This kind of state turns out to be sufficient to implement things like
Falstad’s Circuit.js!  (However, such simulations additionally benefit
from some kind of way to maintain their simulation state from frame to
frame, even when there is no user interaction to react to; for the
time being I will ignore this.)

Suppose we call this new persistent state, which can change in
response to things like clicks and keystrokes, the “fragment”; it’s
analogous to the #fragment in an URL on the WWW.  Accordingly, it
provides the surrounding framework with the freedom to measure its
persistent memory consumption, pause it, save a fragment, go back in
time by reverting changes to the fragment (undo), explore alternatives
from an earlier fragment (nonlinear undo), and copy and paste the
fragment to somewhere else in the document.  This is sufficient for
things like sketching illustrations, self-contained circuit modeling,
or doing geometrical constructions.  Indeed, given camera access, it
could even be sufficient for taking photos.  (There’s no reason the
fragment needs to be limited in size like URL fragments traditionally
are.)

The fragment itself is part of the source format document, just a part
that can be edited by the widget’s embedded code, subject to the
restrictions above about undo and the like.

Methods other than “paint” and “react” could provide requested layout
sizes or render the “widget” as a series of boxes rather than a single
window onto a canvas.

However, so far all of this focuses on applet-like content: a
calculator, compass-and-straightedge interaction, or circuit
simulator, displayed in a window with text flowed around it, or
perhaps overlapping part of it.  It doesn’t cover the kind of
interaction you’d want for data visualization, much less a general
computing platform: you want that calculated result to be accessible
for further calculations elsewhere in the document!  And you want to
be able to feed the circuit you’ve modeled to other analysis functions
that you write on the fly.  You want the data to be open and
accessible, not sealed inside an opaque Actor.

Darius Bacon points out that if the “fragment” state is some more
structured thing, such as a state of, say, a relational database, it
might be easier to deal with the opacity problem.  Maybe it would be
easy enough to say something like `drawing2.points[3].x` elsewhere in
the document.  (Formats other than relational data might be usable
too, such as JSON structures, but they tend to vary more over time as
navigational data is included.)

### Blossoming forms ###

XXX rewrite

Interactive blocks, HTML forms, BASIC with line numbers, and HP 3000
terminals suggest a somewhat unrelated approach.  I tried to write a
FORTRAN program on the HP 3000 that the local computer museum got up
and running.  An interesting thing about the HP 3000 terminals is that
they can do local editing, and apparently text files in their system
have line numbers, like in old BASICs.  So, the editor on the host is
a pretty dopey line-mode thing similar to ed, but it includes the line
numbers before the lines it prints out.  So, locally to the terminal
you can go up with the arrow keys into the scrollback buffer and edit
one of those lines, interactively, inserting and deleting in a WYSIWYG
way, and hit enter to send it back to the host.  When you send it back
to the host it has the line number still attached, and the editor
interprets that as a command to replace the contents of that line
number.

GW-BASIC did this too.  Maybe Applesoft BASIC too?

HTML forms are kind of the same thing except that the line numbers are
words and they're hidden.  You could imagine an interactive block
that's sort of similar to an HTML form but maybe without the
submission delay to run code to see results, and you could imagine it
having buttons in it that, when clicked, blossom out into new nested
formlets there in place.  As long as all the code can do is display
its results, or blossom out into more little bomblets, the degree of
danger is pretty limited.

However, can this approach really handle things like sketching with
the mouse or a stylus, or schematic capture?

### Naked objects ###

XXX

Thanks
------

To Darius Bacon for discussion of these ideas.
