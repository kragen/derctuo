Here’s an interesting idea for how to do Derctuo: a giant WYSIWYG
document whose source format is a plain text file including data,
code, text, and formatting in a single document, potentially of 128
mebibytes or more; but with computational output rigidly segregated to
a cache management system.

Precedents
----------

The immediate inspiration for this is Danny O’Brien’s “Life Hacks”
ethnographic research finding the widespread use of One Huge Text
File.  He found that many of his interviewees maintained all their
notes in a single humongous text file, which they navigated by text
search.  On a modern computer, Emacs incremental-search is capable of
searching through hundreds of megabytes per second, so it’s rare to
even need any indexing.

Eric Raymond’s “Volks-Hypertext” browser for the Jargon File
demonstrated how to improvise a fairly instantaneous hypertext system
atop a large text file: the text file was rendered in more or less the
usual way, but keywords in curly braces like “{grok}” were treated as
links to a line beginning with “:grok:”, and the file was preprocessed
to generate an index of all such lines after the fashion of ctags,
with byte offsets stored.  Searching the index file and jumping to a
given byte offset was reliably fast, even in MS-DOS on a 386.

The cult semistructured database askSam has barely more structure: an
askSam file is a collection of records, which are just free text
strings up to a few kilobytes in size, with fields defined by
searching for the field name followed by square brackets — everything
on top of that is added by the askSam query language.  A full-text
index makes relatively powerful queries acceptably fast.

Darius Bacon’s Doe and Halp systems automatically re-evaluate all the
specially-marked code in a document upon demand, placing the resuls of
each snippet after the snippet itself.

Org-mode adds a little bit of lubrication to text-file viewing: the
Emacs outline-mode ability to collapse and expand sections of the
file, but with more pleasant keybindings.  And it also has magic
syntax for inserting hyperlinks: [[http://example.com/][example URL]]
displays just as an underlined “example URL”, but links to the given
URL, and it also supports links to places within the file.  Org-mode’s
“src blocks” offer the possibility to
[display textual or graphical output inline in the editing buffer](https://orgmode.org/manual/Results-of-Evaluation.html#Results-of-Evaluation).

Cassowary is a constraint-based layout system that offers perhaps a
bit less power than CSS, but has extremely efficient algorithms to
execute it.  (I haven’t actually tried it.)  TeX, too, has extremely
efficient layout algorithms which also produce somewhat nicer results
than CSS.

Before Microsoft Windows, WordPerfect was the most popular word
processing software, and its users’ favorite feature was a thing
called “Reveal Codes”, which split the screen into one half with the
WYSIWYGish text you were editing at the top and a complete
representation of the word processor’s underlying representation at
the bottom, with formatting markup displayed in between bits of text.
This made it easy to see why your document was formatting incorrectly
and fix it.

Lotus 1-2-3 displays the tabular output of a program written by the
user by defining formulas in cells.  It analyzes the dependencies
between the cells to discover as safe dependency order to recalculate
them in when there is a change, and it only displays the source code
of the cell you are editing at a given moment.  It imitated VisiCalc,
the “killer app”, but its dependency-order recalculation was new.

Jupyter’s “notebook interface” is an enhanced REPL which permits the
inline display of graphics, text formatted with LaTeX or HTML, etc.,
as results of the REPL commands (“cells”).  It is accessible via HTTP
or HTTPS, allowing people to share code easily.  Also, it stores the
output in the same text file as the code in the cells, even when it is
graphical or irreproducible.  Jupyter has become the standard
interface to programming for an enormous number of people nowadays.
But it has some serious drawbacks: the output displayed may not be up
to date with the code in the file, re-evaluating the whole file may
not be safe (it’s common for people to put utility scripts in
notebooks that do things like wipe a database), the output being
interpolated into the source code makes the notebook files bulky and
difficult to version-control with systems like Git, it’s awkward to
reuse code, and normally you have to start out the notebook with a
bunch of preliminary noise like module imports.

ObservableHQ is an exploration of how the notebook interface could be
improved.  It uses a slight extension of JS as its language, its cells
each define a single value, and like Lotus 1-2-3, they are evaluated
in dependency order.

R-Markdown XXX

Explorable explanations XXX

make XXX

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
