If you read down the output column of the NOR truth table, you get
1110, binary 14.  The corresponding column of the AND truth table is
0111, binary 7.  In C-derived languages, 14 ^ 7 = 9, binary 1001, the
output column of XNOR’s truth table, and 14 & 7 = 5, binary 0110, XOR.
Correspondingly (A NOR B) XOR (A AND B) = A XNOR B, and (A NOR B) AND
(A AND B) = A XOR B.

In this way on a modern CPU we can combine truth tables of up to 6
inputs in a single instruction, computing the result of combining two
Boolean functions with a Boolean operator.  We could imagine building
up a database of optimal circuits for all Boolean functions of up to,
say, 5 inputs (32 bits per truth table, thus 4'294'967'296 possible
functions.)  Then, if we want to know how to compute any of these
functions, we can just look up the optimal circuit in the database.
We might have different databases for different design criteria; for
example, the circuit with the smallest number of NAND gates might not
be the one with the smallest propagation delay, and if we additionally
have AND or XOR gates we can produce smaller circuits in many cases.

It might be reasonable to build such a database for 6 or more inputs
if we could exploit some kind of simple normalization.  Some
functions, such as XOR and “threshold” functions like AND, OR, and
majority, don’t care if you permute their inputs, but other functions
do.  For three inputs, for example, ~A & (~B | C) gives truth table
0xd0, but other permutations of the same inputs give truth tables
0xb0, 0xc4, 0x8c, 0x8a, and 0xa2.  With five inputs you could have as
many as 5! = 120 functions that are equivalent under permutation of
inputs, and presumably most possible functions don’t have the kind of
special symmetry that XOR have, so you could imagine that taking
advantage of such input permutations would reduce the database size by
two orders of magnitude.  Then, before probing the database, you’d
have to permute the bits in your truth table into the normal order — a
simple criterion would be to take the truth table with the numerically
lowest value, in this case 0x8a, (~C & (~B | A)).

Another kind of normalization that would be useful in many cases is a
different kind of bit permutation: negating some or all of the inputs.
If you negate the most significant bit of the input, for example, you
swap the first and second halves of the truth table.  In contexts
where the negated input is just as easily available as the non-negated
input, this negation comes for free.  Even when it doesn’t come
absoutely for free, this may be worth doing, because the cost is not
large in many contexts.  A counting argument suggests that this
reduces the number of possibilities greatly: for 5 inputs we have 2³²
possible functions.  Of these, some depend on all of their inputs,
while others depend on 4 inputs or less.  The ones with 4 inputs or
less can be expressed with a 4-input truth table plus a position (0,
1, 2, 3, or 4) for the ignored bit.  There are only 2¹⁶ 4-input truth
tables, and so only 5·2¹⁶ 6-input truth tables that ignore a bit;
that’s less than 2¹⁹, which is 1/8192 of the total search space.  The
ones that depend on all 5 inputs usually (handwaving here!) have 2⁵ =
32 versions equivalent under input negations, and (I think) all have
at least 2 equivalent versions.  So we should expect a reduction of a
factor of at least 2 and I think nearly 32 in the database size by
this approach.

That’s not a rigorous argument, but it is at least strongly
suggestive.  Moreover I think these two kinds of normalization are
complementary, and we should get at least a factor of 1000 database
compression by combining them.

Also, of course, before probing the database we can check to see if we
*do* have any don’t-care inputs.  If so, we can probe a much smaller
table, probably in RAM.

I thought about also tabulating all the especially low-cost circuits
for a larger number of inputs, but I think it may not be practical.
Consider tabulating low-cost circuits with 6 bits of input: you need
at least 5 minimal-cost gates, if they’re binary gates (or your
circuit has less than 6 bits of input), and so at a minimum you have
the binary trees on 6 inputs, maybe multiplied by the 5th power of the
number of types of gates.  I think you exceed the number of possible
5-bit truth tables by a lot rather soon.  (But I haven’t done the
calculation.)

If you have a 32-entry truth table to probe for that contains a few
don’t-care entries, the brute-force way to handle them is to probe the
database for the 2, 4, 8, 16, etc., entries they correspond to,
normalizing each possibility in turn.  If the database *isn’t*
normalized in the way I described earlier, you may be able to get some
mileage out of contiguity properties of indices: by permuting the
truth table so that the don’t-care bits are toward the end of your
search key, all the entries that could match will be physically close
together in the database index.  This would permit larger numbers of
don’t-care entries in the search key without totally losing locality.

As for actually building the database, a simple approach is basically
Dijkstra’s algorithm, a breadth-first search using a queue: initially
enqueue the trivial circuit (for example, for five inputs, a circuit
with nets n0=0, n1=1, n2=in0, n3=in1, n4=in2, n5=in3, n6=in4), and
upon dequeueing a circuit, do the following:

- compute the truth table of the last net and the cost of the circuit;
- normalize the truth table;
- check to see if the normalized truth table is already in the
  database with an equal or lower cost, and add it if not;
- compute all possible single-gate extensions of the circuit (e.g., n6
  = n2 NAND n4) and enqueue them.

Probably some kind of circuit-normalization step would be useful to
avoid enqueuing trivial permutations of already-enqueued or even
already-processed circuits.  Also, if the cost metric is something
more complex than just “number of gates”, you might want to use a
priority queue (by cost) rather than a regular FIFO queue.  To find
out when you’re done, you can maintain a second database of
still-unachieved normalized 5-input truth tables, removing items from
it as you find them.

I thought about trying to just do the graph traversal on truth tables,
stored for example as 64-bit integers, rather than circuits — so,
going back to my first example, if you knew that computing the truth
table 14 takes c(14) = 1 gate, and computing 7 takes c(7) = 1 gate,
then you can XOR them together (assuming XOR is one of your primitive
gates) and get 9, with cost c(14) + c(7) + 1 = 3 gates.  This runs
into two problems:

- It doesn’t take structure sharing into account, which becomes
  important for circuits of more than two inputs, so the costs it
  computes are only upper bounds.
- You have to iterate over the entire database of known truth tables
  every time you add a new truth table in order to enqueue all the
  successor circuits, and it isn’t obvious how to do that in an
  efficient way.

So much for tabulating forward-chaining search results for a
meet-in-the-middle attack.  How can we chain *backwards*, though?

One omnipotent approach, ably explained by Darius Bacon in [The
Language of Choice][0], is the binary decision diagram: by choosing a
Boolean variable to split the universe in half with first, we reduce
the number of possibilities by half.  So if we have a database of
optimal circuits for all 5-bit-input Boolean functions, and we want a
6-bit Boolean function, we can pick one of the 6 bits to split our
truth table in half with, probe the database twice, and combine the
results with a MUX.

[0]: https://codewords.recurse.com/issues/four/the-language-of-choice

Moreover, we can quite plausibly do this six times to see if any of
the six gives us a better result.  On an SSD each probe might take
100 μs, so this might take 1.2 ms, while on spinning rust it might
take a second.

This kind of MUX-based approach is probably reasonable up to somewhere
around 3 more bits of input, so up to 8 bits of input if we have a
table of circuits for all 5-bit functions.  It’s still fast and
guaranteed to work at that point and well beyond, but beyond that I
think it’s going to usually synthesize circuits that are worse than
optimal by an order of magnitude or more.

I’m not sure how else to do backward chaining.  Maybe you could
consider partitioning the input bits into subsets.  You could divide 9
input bits, for example, into 1 bit and 8 others (9 ways), 2 bits and
7 others (36 ways), 3 bits and 6 others (336 ways), or 4 bits and 5
others (4536 ways).  It might turn out that some single-bit function
of those 5 bits can be combined with the other 4 bits to produce the
512-row truth table we’re looking for.

Somehow for backward chaining we need to be searching for *simpler*
component functions, truth tables that are functions of fewer inputs.
Large blocks of zeroes or ones suggest the possibility of AND or OR
with a function of fewer bits — then on the other input of the AND or
OR those bits become don’t-cares.