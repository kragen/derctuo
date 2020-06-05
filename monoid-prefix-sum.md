The parallel prefix-sum or scan algorithm makes it possible to
calculate a prefix sum on N elements in O(log N) time on an unbounded
number of processors.  As Stepanov may have been the first to point
out, this algorithm is applicable to general monoids, although its
performance only remains O(log N) if the monoid operation can be
computed in constant time.

Like unto many other parallel algorithms, parallel prefix sum can be
easily converted into an incremental algorithm through a tricky
time-space switcheroo: we can cache all the values computed during the
algorithm, and upon a small change to the input, we can treat the
values computed from unchanged parts of the input as if they were
values computed on other processors, receiving them from the cache as
if they were received over the network.  This gives us a
logarithmic-time way to incrementally update the reduction of an
arbitrary (constant-time) monoid over an input sequence, since that is
the final element of the scan — for example, the sum is the final
element of the prefix sum.  (Integer sum in particular admits more
efficient implementations, because it is not just a monoid but an
abelian group — in constant time, you can simply add the inverse of
an element that is being removed.  But, for example, semilattice
operations are not so forgiving.)

In a sense any algorithm that produces a result from input data is a
reduction followed by some kind of final postprocessing; the input
data comes in some sequence, and in the degenerate case, the reduction
is just in the free monad, concatenation — the reduction is just the
concatenation, and then the final postprocessing is the algorithm
itself.  But of course that doesn’t give us any parallelism or
incrementality advantages.

Testing associativity in O(N³) time
-----------------------------------

Suppose we do have some kind of interesting iterative processing going
on over the input data, though, formulated in a monoidal way: we have
a lifting operation that maps an input element into a “lifted
element”, a composition operation that maps a sequence of two lifted
elements into a single equivalent lifted element, and perhaps a
postprocessing operation that maps a lifted element representing the
whole sequence into the result we wanted.  But to be able to use it
correctly with the prefix-sum algorithm, we need to be sure the
composition operation is really monoidal, which is to say,
associative.  How can we verify this?

It may not be possible to verify rigorously in all possible cases, but
it is at least reasonably efficient to verify that it is associative
over a given input string of N elements, requiring O(N³) time, using a
dynamic-programming-like algorithm.  The input string contains
N(N+1)/2 nonempty substrings, each of which can be divided into two
nonempty substrings in less than N ways.  So we create an array of
lifted elements for these N(N+1)/2 nonempty substrings, and we
calculate the reduction value for each of these substrings in all
possible ways.  For substrings of a single element, we simply use the
lifting operation.  For each substring of M > 1 elements, we test all
of the possible M - 1 divisions into nonempty substrings by applying
the composition operation M - 1 times; they should all produce the
same value, which we then store into the array.

For “reasonable” composition operations, it should be possible to do
this test for sequences up to lengths of a few hundred in under a
second, perhaps a few thousand.  This does not of course amount to a
proof that the operation is monoidal, but it may be a fairly
convincing test.

### A trivial example: canonicalization of a binary carry-save sum ###

So, for example, the string ABCD, of length 4, has the 10 nonempty
substrings A B C D AB BC CD ABC BCD ABCD.  Suppose that, for some
inexplicable reason, we want to reduce this string with the function
λs c . s × 2 + ord(c), which takes the previous state, multiplies it
by two, and adds the ASCII value of the input letter to it, starting
with an initial state of 0.  Our “lifted elements” are an ordered pair
of integers (n, k), representing the function λs.s × n + k.  The
lifting function maps a letter c to the pair (2, ord(c)).  The
composition function maps two pairs (n1, k1), (n2, k2) to the
equivalent function (n1 × n2, k1 × n2 + k2).  So A becomes (2, 65), B
becomes (2, 66), etc.; AB becomes (4, 196), BC becomes (4, 199), CD
becomes (4, 202); ABC can be computed either as A + BC = (2 × 4, 65 ×
4 + 199) or as AB + C = (4 × 2, 196 × 2 + 67), giving in either case
(8, 459); and ABCD can be computed either as A + BCD, AB + CD, or ABC
+ D, giving the same result (16, 986) in all three cases.

(In this case, the postprocessing operation amounts to simply taking
the second item of the tuple.)

Ropes
-----

Thus if we annotate rope nodes with lifted elements, we can
incrementally update the monoidal reduction of the whole rope even
after insertion and deletion operations; it isn’t necessary for the
lifted elements to correspond to elements whose counts are powers of
two.  I think Raph Levien has done this for his Xi editor.

CRDTs
-----

By applying this incremental monoidal reduction approach to logs of
historical events with a well-defined total sorting order, we can
derive a wide variety of efficient CRDTs.  We use the standard union
CRDT on a set of historical events, merging newly-received events into
a rope of already-received events and recomputing the lifted elements
on the updated nodes.  This allows us to efficiently recompute the
monoidal reduction over the updated dataset.

In particular, we can derive common CRDTs in this way, such as a
dictionary updated by upserting and deleting key-value pairs;
Okasaki’s FP-persistent data structures are likely useful here.  (I
suspect this is actually how Datomic works.)

Further efficiency issues
-------------------------

Of course, if the lifted elements contain some arbitrarily large data
structure, or if the composition operation or postprocessing is
arbitrarily expensive, then you can lose the efficiencies.  Running
the above example composition function over the input string
“ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz” gives the
lifted value (4503599627370496, 297237575809105796), each value
growing by one bit per additional input character.

If memory is sufficiently expensive, or recomputation from scratch is
sufficiently cheap, it may not be worthwhile to cache these lifted
values for every element of the input sequence; it might be sufficient
to cache one out of every 32–2048 values, thus saving 97–99.95% of the
cache space while still limiting the work to recompute a value at a
given location to the redundant reprocessing of 31–2047 input
elements.

I think some of these ideas originated in discussions with Darius
Bacon, but I can’t remember.

Two text-editor-oriented examples
---------------------------------

### Columns ###

Text editors commonly want to know what column of the screen to
display a character in, which depends on how many characters precede
it on the same line.  (And, possibly, how wide the screen currently
is, and how wide those preceding characters are.)  In the simple case
we can compute this from the beginning of the editor buffer with a
simple loop:

    size_t col = 0;
    for (int i = 0; i < point; i++) {
        col++;
        if (buf[i] == '\n' || col == screen_width) col = 0;
    }

The state of `col` after an iteration of the loop depends on its state
before that iteration and on `buf[i]`; it is either 1 + old `col` or
0.  Arbitrary compositions of such iterations give us either (n + old
`col`) % `screen_width` or n % `screen_width`, so those are our
monoidal “lifted values”.  It’s reasonable to store only one out of
every 1024 or so such values, and we could probably use the sign bit
to distinguish between them (at the expense of not being able to
handle a single buffer occupying more than half of your address
space), so we can probably use a single pointer-sized word for a
lifted value.

In C we probably have to manually compile that into fiddly integer
manipulation, so here is the composition function in OCaml, for
clarity:

    type lifted = Absolute of int | Relative of int
    let compose left right = match (left, right) with
    | _,          Absolute n -> Absolute n
    | Absolute m, Relative n -> Absolute (m + n)
    | Relative m, Relative n -> Relative (m + n)

    # compose (Absolute 8) (Absolute 5) ;;
    - : lifted = Absolute 5
    # compose (Absolute 8) (Relative 5) ;;
    - : lifted = Absolute 13
    # compose (Relative 8) (Relative 5) ;;
    - : lifted = Relative 13
    # compose (Relative 8) (Absolute 5) ;;
    - : lifted = Absolute 5

You can fire off a background thread upon opening a file to
precalculate these values for the whole file, and then incrementally
maintain them thereafter in the face of insertions and deletions.
Moreover, since one of the cases of the composition function doesn’t
even depend on the left side, you can calculate it lazily *backwards*
from a given position in the file — you need only search backwards
until you find the beginning of a line.

### Syntax highlighting ###

Text editors commonly do syntax highlighting based on data like which
identifiers are in scope at a given point, and what their types are,
and at least a tokenization of the source code, though sometimes not a
full parse (since, after all, we don’t want our syntax highlighting to
entirely disappear if the input has a parsing error somewhere off the
screen).  Lexical scanning of the input is usually done with a DFA, or
a slight extension of a DFA, for example to accommodate shell-script
here-documents (the input for `<<wibble` ends at the first line that
says `wibble`) or Lua fat parentheses (a string starting with
`[=======[` continues to the next `]=======]`, where 7 has any value).

Considering purely the DFA case, we can consider the “lifted value” of
a string to be the mapping from the state the DFA is in at the
beginning of the string to the state that it’s in at the end of the
string.  If the DFA has 16 states or less, we can represent such a
mapping in a 64-bit register, since it contains 16 nybbles.

In this case the mapping tends to pretty quickly converge on a
function that ignores its input, although there are exceptions.  C
doesn’t have nested comments or multiline strings, so as soon as you
see a newline you know you’re not in a string, and as soon as you see
a `*/` outside of a string you know you’re not in a comment, so in C
typically you can calculate the precise DFA state before going back
too far.  OCaml, by contrast, does have nested comments, so, in
theory, to highlight any position in the file, you have to go back to
the beginning of the file to ensure you’re not inside of one.  You
can’t tokenize it with a strict DFA.

The identifiers that are in scope at a given point can similarly be
adduced, typically, by calculating a “lifted value” that is either a
stack of identifiers that are in scope at various levels of scope
stacked on top of whatever was in scope at the beginning of the
substring, or a count of scopes to pop.  So, for example, in a C-like
language, this string

    {
        int x = 3;
        {
            int y = 4;
            int z = 5;

might lift to the stack effect [{}, {x: var}, {y: var, z: var}],
augmented with a bit of scanner information; meanwhile the string

                }
            }
        }

might lift to an instruction to pop three stack levels of
declarations.

Languages like JS that have entities without forward declarations
cannot be handled in this fashion; they need a preliminary pass over
the whole file first to index the declarations.
