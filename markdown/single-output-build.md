Some build systems and dependency systems support build steps that
produce multiple outputs.  Make, on the other hand, identifies each
build step with a single build artifact produced by that build step.
This is a better approach.

An apparent benefit of multiple-output build steps is efficiency:
perhaps the same compilation that produces an object file also
produces, for example, a listing file, and producing them separately
requires essentially running the compilation twice, with the same
optimization settings (and all potential sources of nondeterminism
removed.)  The solution for this problem is to make an *output
directory* be the resulting build artifact, containing both files.

The dependencies (inputs) of a build step can be determined by
interposition, for example watching the system calls performed in
order to find out what files are being opened.  If the build step
succeeds or fails at some point, then as long as it is deterministic,
we can be sure that it will succeed or fail again with precisely the
same results as long as none of the environment it observed while
running has changed.  In particular, this means that it is okay if it
*would have* read some other potential input file if it had not
encountered an earlier error — changes in that other potential input
file will not change the error.  And it is perfectly okay to read
references from one input file, such as foo.c, to another, such as
foo.h; as long as foo.c does not change, the resulting dependency set
remains static.

Multiple outputs of a build step are, by contrast, messier.  What
happens if two separate possible build steps can create the same file?
What happens if a build step creates a file on one occasion, but due
to a change in its inputs, not on another?  It’s better to steer clear
of such messy issues.

Although it may be most convenient to support a traditional filesystem
API for producing build artifacts, it isn’t necessary.  Suppose we are
constrained to produce one file per rule, as the standard output of a
build script, but the build step runs inside an isolated filesystem
bubble whose contents are discarded once it finishes.  Then we can
handle the above listing+object case as follows, using Make syntax but
for convenience with inputs inferred as described above:

    foo.tar:
        gcc -g -Wa,-adhlns=foo.lst -c foo.c
        tar cf - foo.lst foo.o

    foo.lst:
        tar xf foo.tar foo.lst
        cat foo.lst

    foo.o:
        tar xf foo.tar foo.o
        cat foo.o

You can do the same thing within a single process, but it generally
takes more than two short lines of code to express it.  And you could
imagine a memory-centric version of this where the “foo.tar” output
was in the format of a segment of (sharable, read-only) memory, and
foo.lst and foo.o were “subsegments” of it.  So this approach doesn’t
depend on the use of the filesystem.

Why might you want to split out a build artifact into multiple pieces
this way?  After all, any computation you can do on the basis of foo.o
above can also be done on the basis of foo.tar.  I think there are two
reasons: decoupling and caching.

The linker should not be coupled to the fact that the compiler is
generating a listing file.  Rather, it should be insulated from that
information.  It should not have to fish the object code it’s
interested in out of a larger file containing mostly things it’s not
interested in.  That’s decoupling.

Moreover, if you make a change to the source code or the build script
that doesn’t change the object file, only the listing file, it would
be nice to avoid rerunning the linker.  If the linker doesn’t even
open the listing, we know it can’t depend on its contents.  So we can
use the linker’s cached output.
