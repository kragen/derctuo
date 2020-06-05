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
the final element of the scan --- for example, the sum is the final
element of the prefix sum.  (Integer sum in particular admits more
efficient implementations, because it is not just a monoid but an
abelian group --- in constant time, you can simply add the inverse of
an element that is being removed.  But, for example, semilattice
operations are not so forgiving.)

In a sense any algorithm that produces a result from input data is a
reduction followed by some kind of final postprocessing; the input
data comes in some sequence, and in the degenerate case, the reduction
is just in the free monad, concatenation --- the reduction is just the
concatenation, and then the final postprocessing is the algorithm
itself.  But of course that doesn't give us any parallelism or
incrementality advantages.

Suppose we do have some kind of interesting iterative processing going
on over the input data, though, formulated in a monoidal way: we have
a lifting operation that maps an input element into a "lifted
element", a composition operation that maps a sequence of two lifted
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

So, for example, the string ABCD, of length 4, has the 10 nonempty
substrings A B C D AB BC CD ABC BCD ABCD.  Suppose that, for some
inexplicable reason, we want to reduce this string with the function
λs c . s × 2 + ord(c), which takes the previous state, multiplies it
by two, and adds the ASCII value of the input letter to it, starting
with an initial state of 0.  Our "lifted elements" are an ordered pair
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

For "reasonable" composition operations, it should be possible to do
this test for sequences up to lengths of a few hundred in under a
second, perhaps a few thousand.  This does not of course amount to a
proof that the operation is monoidal, but it may be a fairly
convincing test.

Thus if we annotate rope nodes with lifted elements, we can
incrementally update the monoidal reduction of the whole rope even
after insertion and deletion operations; it isn't necessary for the
lifted elements to correspond to elements whose counts are powers of
two.  I think Raph Levien has done this for his Xi editor.

By applying this incremental monoidal reduction approach to logs of
historical events with a well-defined total sorting order, we can
derive a wide variety of efficient CRDTs.  We use the standard union
CRDT on a set of historical events, merging newly-received events into
a rope of already-received events and recomputing the lifted elements
on the updated nodes.  This allows us to efficiently recompute the
monoidal reduction over the updated dataset.

In particular, we can derive common CRDTs in this way, such as a
dictionary updated by upserting and deleting key-value pairs;
Okasaki's FP-persistent data structures are likely useful here.  (I
suspect this is actually how Datomic works.)

Of course, if the lifted elements contain some arbitrarily large data
structure, or if the composition operation or postprocessing is
arbitrarily expensive, then you can lose the efficiencies.  Running
the above example composition function over the input string
'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz' gives the
lifted value (4503599627370496, 297237575809105796), each value
growing by one bit per additional input character.

I think some of these ideas originated in discussions with Darius
Bacon, but I can't remember.
