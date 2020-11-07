I've written a little bit previously about a sort of pattern-matching
Prolog, where instead of dealing with explicitly given relations:

    father(fred, mary).
    parent(X, Y) :- father(X, Y).
    parent(X, Y) :- mother(X, Y).
    ancestor(X, Y) :- parent(X, Y).
    ancestor(X, Y) :- parent(X, Z), ancestor(Z, Y).

you deal with strings of text, such as lines in a CSV file or a chat
transcript, or sentences:

    Fred is Mary's father
    Mary is John's mother

    'Foo' is 'Bar''s father
    ----
    Foo is Bar's parent

    'Foo' is 'Bar''s mother
    ----
    Foo is Bar's parent

    'Alice' is 'Bill''s parent
    ----
    Alice is Bill's ancestor

    'Alice' is 'Carol''s parent
    Carol is 'Bill''s ancestor
    ----
    Alice is Bill's ancestor

From this we can deduce:

    Fred is Mary's parent
    Fred is Mary's ancestor
    Mary is John's parent
    Mary is John's ancestor
    Fred is John's ancestor

In Prolog-style top-down search, this is kind of tricky, since you
kind of have to guess which inference rules can match which patterns,
but in Datalog bottom-up inference, there's no difficulty; each newly
inferred sentence need only be matched against the premises of all the
rules to see if it enables additional sentences to be inferred.  This
won't be nearly as efficient as Prolog, but that's fine.  It's
probably efficient enough for many uses just by brute force, and a
little indexing on infrequent words should go the rest of the way.

I was thinking about this last night and got really excited.
Interactively or in batch mode, a UI for such a database can add a
table of results underneath each set of premises, which are
distinguished from ground facts by containing variables.  (Above I've
marked these with apostrophes, but other syntax might be better,
especially for natural languages containing contractions.)  But you
can also add negation, using the standard Datalog stratification
approach (done dynamically rather than statically):

    'Aaron' is indicted
    \+ Aaron is guilty
    ----
    Aaron is falsely accused

and you can add aggregate functions:

    'X' has line item 'Y'
    'Y' costs 'Z'
    ----
    X totals =total(Z)

where implicitly we are quantifying over all Y (or, I guess, reducing
over all Y) because Y does not occur in the rule's conclusion outside
of an aggregate.

And of course you can have other formulas as well:

    'C' is a cylinder
    C has radius 'r'
    C has height 'h'
    ---
    C has volume =(pi r² h)
    C has surface area =(2 pi r² + 2 pi r h)

    'Something' is made of 'unobtainium'
    Unobtainium has density 'D'
    Something has volume 'v'
    ----
    Something has mass =(D*v)

It's probably better to abbreviate this:

    'C':
        is a cylinder
        has:
            radius 'r'
            height 'h'
    ---
    C has:
        volume =(pi r² h)
        surface area =(2 pi r² + 2 pi r h)

    'It':
        is made of 'unobtainium'
        has volume 'v'
    Unobtainium has density 'D'
    ----
    It has mass =(D*v)

In this form the system is sort of unidirectional; it can infer the
volume of a cylinder from its radius and height, but it can't infer
its radius from its volume and height.  Spreadsheets use a special
"goal seek" interaction for this; you identify which cells are "design
variables" the optimizer can twiddle and which cell you want to give a
given value, and it twiddles the design variables to approximate it as
closely as possible.  This could be supported syntactically, using the
same syntactic distinction between variables and constants as in
premises:

    c1:
        is a cylinder
        has height 32cm
    *seek*:
        c1 has:
            volume 1 m³
            radius 'r'

This doesn't give you the whole bidirectional power of constraint
solvers, but it's very simple to use and implement, and perfectly
adequate for many computations I do in Derctuo.

Tabular output can go beyond the simple column-per-variable default;
you can, for example, specify a sort key, change the order of columns,
change the formatting of columns, pivot one or more variables to be
the column headers for the others, hide columns, etc.  In an
interactive system, you could add rows to the table as a form of data
entry.

A non-interactive system can be implemented that just reads in a text
file and spews out an augmented version of it.
