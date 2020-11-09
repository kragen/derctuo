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

I was thinking about this last night and got really excited.  I think
it might offer an easily usable system with enough expressive power to
be useful.

Basic UI
--------

Interactively or in batch mode, a UI for such a database can add a
table of results underneath each set of premises, which are
distinguished from ground facts by containing variables.  (Above I've
marked these with apostrophes, but other syntax might be better,
especially for natural languages containing contractions.)  In batch
mode, it could simply process a text file and produce a file annotated
with deductions and query results.

Negation
--------

But you can also add negation, using the standard Datalog
stratification approach (done dynamically rather than statically):

    'Aaron' is indicted
    \+ Aaron is guilty
    ----
    Aaron is falsely accused

I'm not totally clear on how this works without doing the kind of
textual rule analysis that I said above was difficult.

Aggregate formulas
------------------

Also you can add aggregate functions:

    'X' has line item 'Y'
    'Y' costs 'Z'
    ----
    X totals =total(Z)

where implicitly we are quantifying over all Y (or, I guess, reducing
over all Y) because Y does not occur in the rule's conclusion outside
of an aggregate.

Aggregates include =sum(), =total(), =mean(), =stdev(), =max(),
=min(), =list() (which separates items with commas) and =any(), which
just picks one of the values in some unspecified way.

=argmax() and =argmin() are aggregates taking *two* arguments: the
first is the thing to be returned, while the second is the thing to be
maximized or minimized.  So, for example, to get the material that
provides the lowest cost, you say =argmin(material, cost).

Scalar formulas
---------------

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

Here because `D` and `v` occur outside of an aggregate function you
are not aggregating over all the densities and volumes.  If it happens
that some object has two volumes and is made of two materials, each of
which have two densities, then the system will deduce eight masses for
it.

Abbreviation
------------

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

Goal seek
---------

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

Libraries
---------

You probably want to be able to import library modules so that you
don't have to explain things like cylinders and densities in every
database.  Probably the best way to do this, in a textual system, is
to stick a line in the file saying something like

    :use circuits geometry shapes

But this should probably be at the end of the file.

Existentials
------------

If you say that some object is a cylinder, you probably don't want the
system to posit a material from which it is made.  But there might be
cases where you do want such deductions:

    'x' is a car
    ----
    'something' is the steering wheel of x

Here we have a free variable in the conclusion of the rule, with the
meaning that the system is entitled to make up an object to fill that
role if nothing else turns up.  This involves a sort of negation.

UI affordances
--------------

Tabular output can go beyond the simple column-per-variable default;
you can, for example, specify a sort key, change the order of columns,
change the formatting of columns, pivot one or more variables to be
the column headers for the others, hide columns, etc.  In an
interactive system, you could add rows to the table as a form of data
entry.

A non-interactive system can be implemented that just reads in a text
file and spews out an augmented version of it.

Multiple words and nesting
--------------------------

All of the above is entirely without nesting, and the lack of nesting
is one of the great UI benefits of logic programming in general.  But
sometimes you do need nesting in order to be able to correctly reason
about complicated propositions, especially without existentials.

A really simple approach, which doesn't go far, is to allow variables
to match arbitrary sequences of words instead of single words:

    Bob Smith is a person
    Mary Smith is a person

    'Someone' is a person
    ----
    Someone has skin

    'John' 'Doe' is a person
    'Richard' Doe is a person
    ---
    John Doe is related to Richard Doe

From this we can infer, among other things, that Mary Smith has skin
and Mary Smith is related to Bob Smith.

The simplest possible approach to nesting would be *not* allowing
variables to match arbitrary sequences of words *containing unmatches
parentheses*.  That way you could use parentheses to supply arbitrary
nesting structure.

It might be desirable for a variable to *not* match multiple words by
default.  This is partly a usability question that ought to be studied
by studying users.

### Regexps ###

If you're trying to apply this kind of tool to parsing text that it
wasn't intended for, it might be convenient to specify a regex to
constrain the matches.

    start 'year/\d+/'-'month/\d+/'-'day/\d+/'
    ---
    Session began 'day'.'month'.'year'

Denesting
---------

The abbreviation facility above suggests writing:

    I should buy:
        red peppers
        bananas
        apples

to create three facts.  But what if we're trying to assimilate
something like

    I should buy red peppers, bananas, apples

Then maybe it would be useful to be able to write a rule with a
premise like

    I should buy 'food...'

to match three times food=red peppers, food=bananas, food=apples,
vaguely similar to Scheme `syntax-rules`.

Syntax
------

Especially for a textual system, syntax is important for UI.  Some
alternatives to the strawman syntax above:

- Alternative syntax for variables.  Above I've only stuck sigils on
  variables to indicate their variable nature the first time they
  occur, but it might be worthwhile to use the sigil every time for
  readability; Tcl and bash seem to suffer in usability compared to
  PHP and Perl's more universal sigil usage.  Here are some possible
  alternatives:

        'It' is made of 'unobtainium'   # example above
        «It» is made of «unobtainium»   # still harder and safer
        <It> is made of <unobtainium>   # common metavariable syntax in grammars
        `It` is made of `unobtainium`   # e.g., SQL
        "It" is made of "unobtainium"   # also SQL but less weird; harder to
                                        # type than '' but safer with contractions
        It is made of Unobtainium       # Prolog
        IT is made of UNOBTAINIUM       # variant
        it IS MADE OF unobtainium       # variant, often used informally for, e.g., SQL
        It$ is made of unobtainium$     # BASIC
        $It is made of $unobtainium     # Perl/PHP, taken from BASIC and sh; also Tcl
        @It is made of @unobtainium     # Perl variant
        .It is made of .unobtainium     # minimal line noise variant
        :It is made of :unobtainium     # Logo, almost as calm
        It :is :made :of unobtainium    # Lisp/Ruby
        It 'is 'made 'of unobtainium    # Lisp
        ,It is made of ,unobtainium     # Lisp quasiquoted
        ?It is made of ?unobtainium     # Lisp-family logic languages
        It? is made of unobtainium?     # variant
        ¿It? is made of ¿unobtainium?   # Spanish variant
        {It} is made of {unobtainium}   # various templating languages
                                        # including Python .format and f''
        #{It} is made of #{unobtainium} # Ruby's equivalent
        [It] is made of [unobtainium]   # easier to type on standard keyboard than {}
        (It) is made of (unobtainium)   # the remaining ASCII nesting delimiters
        %It% is made of %unobtainium%   # MS-DOS batch
        %It is made of %unobtainium     # variant
        ¤It is made of ¤unobtainium     # variant
        |It| is made of |unobtainium|   # more little-used delimiters
        It* is made of unobtainium*     # asterisk connotes reference
        It† is made of unobtainium†     # though daggers connote it HARDER
        It... is made of unobtainium... # ellipses connote indefiniteness

    In a multi-font system, we could imagine writing <i>It</i> is made
    of <i>unobtainium</i>, <u>It</u> is made of <u>unobtainium</u>,
    <b>It</b> is made of <b>unobtainium</b>, or <span style="color:
    #666">It</span> is made of <span style="color:
    #666">unobtainium</span> instead.  (Note that if you're viewing
    this on GitLab some of the formatting in this paragraph gets
    mangled by their buggy Markdown parser.)

- Alternative syntax for deduction.  The line of dashes echoes the
  sequent calculus but it's kind of heavyweight, and how many dashes
  do you use, anyway?  Does it matter?  And then there's the question
  of how far its scope extends (above, to the first blank line).  And
  should the premises come before the conclusion, as above, or after
  it?  Here is the original and some strawman alternatives;

            {Alice} is {Carol}'s parent
            {Carol} is {Bill}'s ancestor
            ----
            {Alice} is {Bill}'s ancestor

            {Alice} is {Bill}'s ancestor :-
                {Alice} is {Carol}'s parent
                {Carol} is {Bill}'s ancestor

            {Alice} is {Bill}'s ancestor?
                {Alice} is {Carol}'s parent
                {Carol} is {Bill}'s ancestor

            if:
                {Alice} is {Carol}'s parent
                {Carol} is {Bill}'s ancestor
            then:
                {Alice} is {Bill}'s ancestor

            {A} is {C}'s parent; {C} is {B}'s ancestor |- {A} is {B}'s ancestor

            :A is :C's parent; :C is :B's ancestor { :A is :B's ancestor }

            {Alice} is {Carol}'s parent
            {Carol} is {Bill}'s ancestor
            :. {Alice} is {Bill}'s ancestor

            {Alice} is {Carol}'s parent
            {Carol} is {Bill}'s ancestor
            => {Alice} is {Bill}'s ancestor

    Although I like the one with ":.", the closest ASCII equivalent of
    "∴", I think the last one with "=>", due to deltab, is better.
    They both avoid spurious visual suggestions of nesting, it's
    compact, and there's only one way to do (each of) them.  The
    `premises { conclusion }` idea, also due to deltab, is also very
    nice, but like the turnstile `|-` it clashes somewhat with the
    overall line-oriented style.

- Alternative syntax for formulas.  I think most formulas will
  probably be fairly simple affairs, so it's nice to be able to
  introduce them with just a single character instead of nested
  delimiters; `=total(cost)` beats `[total(cost)]` on visual noise.
  And the `=` syntax is familiar from Excel, having replaced
  Visicalc's `@` syntax (also used in Lotus 1-2-3, though with one
  less period for ranges): `+B1-SUM(C2...C8)`.  Still, you could
  imagine other syntaxes.  ES5 template strings use `${2 * a + b}`.

Queries and reporting
---------------------

Every set of premises is a query whose answer is a table, but it might
be more useful to be able to include only some of these tables in a
formatted output report.

Quantities
----------

Above I've talked about quantities like `32cm` and `1 m³`, which have
units and are expressed in Unicode notation.  This is very valuable
for a lot of the calculations I'm doing.  I'm not sure if you can
implement that within the system or what.

You know what else would be very valuable?  Intervals.  `32cm±5cm`.
`1–1.5m³`.  And gradients: when a value is computed by a formula from
some given data, it would be useful to see what its gradient is in
terms of those givens.  Computing the gradient is of course also very
useful for "goal seek".
