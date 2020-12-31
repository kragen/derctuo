Bloomtags: a Bloom-filter tree for efficient and flexible database queries
==========================================================================

Suppose you have a large file of lines tagged with hashtags and you
want to efficiently iterate over the lines satisfying a given hashtag
intersection.  What kind of index structure supports this?

You could use a tree of Bloom filters with a relatively high
false-positive factor and add additional “synthetic tags” to improve
precision for certain pathological queries.  This seems like it will
probably give reasonable efficiency, and it has some significant
efficiency advantages over existing database indexing approaches for
evaluating some kinds of queries.

The example problem: 8.5 billion lines of data with 8 hashtags each
-------------------------------------------------------------------

For concreteness, let’s suppose you have a tebibyte of 128-byte lines,
each of which is tagged with 8 hashtags, which follow a perfect Zipf
distribution, with the most common hashtag occurring in 25% of all
lines, so the next most common ones are in 12.5%, 8.3̄%, 6.25%, 5%, 4%,
etc., of all lines.  So in total there are 8 gibilines.  Let’s suppose
that the distribution of hashtags is otherwise uniform and
uncorrelated, for example with 3⅛% of the lines being tagged with both
of the two most common tags.

There are perhaps 4 gibihashtags, although the majority are one of the
most common hashtags.  So you can store a hashtag in 32 bits, but you
might be able to get away with a lot less in the average case, so the
8 hashtags per line take up 32 bytes per line.

If the file is divided into 4-kibibyte blocks, there are 256
mebiblocks in the file.  Reading any one of these blocks from a modern
SSD costs about 50 μs (270 μs on the machine I’m using, with
120 megabytes per second throughput giving about a 32-kibibyte
bandwidth-delay product, but it’s second-rate, and most SSDs have both
more bandwidth and many more iops, bringing theirs closer to
4K — which is the smallest request size they support anyway), and
iterating through the 32 lines in it to determine whether they contain
the hashtag; this is less than 100 instructions, so it’ll be
bottlenecked on main-memory bandwidth, which in turn is probably
bottlenecked on SSD bandwidth.

I’m informed that NVMe devices get close to 1 GiB per second with 4k
reads, and PCIe Optane devices can get 2.5 GiB per second (the PCI
controller limit) with 4k reads, implying upwards of 600k iops.  So,
doing the query by sequential scan on an Optane drive would take 400ms
per gibibyte and thus 410 seconds.

Well, a thing we can already do to improve the situation is to
segregate a hashtags column or index elsewhere; it’s only a fourth of
the total file size, so we can fit the hashtags of 128 lines into each
4-kibibyte block.  This would get our query time down to 102 seconds.

Most hashtags are extremely specific, occurring in only a single line.
If we have a query for such a hashtag, it would be nice to be able to
follow a tree of Bloom filters down to the single hashtag-column block
that contains the single line with that hashtag.

Bloom filter background
-----------------------

A Bloom filter is a bit vector.  An *m*-bit Bloom filter for *n* keys
*e*₀, … *e*ₙ₋₁ with *k* independent hash functions *h*₀, … *h*ₖ₋₁ such
that ∀*i*, *j*: *hᵢ*(*eⱼ*) ∈ [0, *m*) is a vector of *m* bits *bₚ*
which are 1 precisely when ∃*i*, *j*: *hᵢ*(*eⱼ*) = *p*, but 0
otherwise.  That’s all!  You can see that if the *hᵢ* are random
enough and *m* is large enough, then for some key *d* not in the set,
you can usually find some bit in the filter that is 0, but would have
been 1 if *d* were in the set; but some false-positive probability
always exists, depending on *k* and the load factor *f* (the fraction
of 1 bits), specifically *f<sup>k</sup>*.  Typical values of the
bits-per-element parameter *c* = *m*/*n* range from 2 to 16, and
typical values of *k* are also about 2 to 16.

    bh = lambda i, e: hash((i+1)*hash(e))  # circumvent Python's weak hash()
    bloom = lambda m, k, e: ([1 if any(bh(i, ej) % m == p
                                       for i in range(k) for ej in e) else
                              0 for p in range(m)], k)
    in_bloom = lambda (bits, k), e: all(bh(i, e)) % len(bits) == 1
                                        for i in range(k))

As [Norm Hardy explains][1], there are a lot of nice tricks you can do
with Bloom filters.  Two of the relevant ones are unioning and
folding.

[1]: http://www.cap-lore.com/code/BloomTheory.html

    def bloom_union((bits_a, k_a), (bits_b, k_b)):
        assert k_a == k_b
        return [ai | bi for ai, bi in zip(bits_a, bits_b)], k_a

You can OR several Bloom filters together, with or without a bit shift
or bit rotation; the result is a Bloom filter with a higher load
factor and consequently a higher false-positive rate that can be
efficiently queried to determine if any of the child filters might be
capable of containing the query key.  With the shift or rotation, you
can also determine which.

You can also *fold* a Bloom filter: take it and OR its two halves
together to get a smaller Bloom filter with one less bit of address,
and also a higher load factor and false-positive rate.  For example,
if you initially compute 64 Bloom filters with a load factor of 1.08%,
you can OR them together to get a single Bloom filter of the same size
with a 50% load factor, or you can fold one of them six times to get a
64×-smaller Bloom filter with the same set of keys as the initial
filter but the same 50% load factor.

The Bloom-filter tree index structure
-------------------------------------

So suppose we take our 256 mebiblocks, each containing 32 lines with 8
hashtags each, and compute a gigantic Bloom filter for each one.  We
divide these into 64 groups of 4 mebiblocks, OR together the filters,
and then rotate-and-OR together all these filters to produce a single
master filter for the whole file with a 50% load factor, which when
queried will tell us which of these 64 groups might contain the key.
If we are satisfied with a 1/128 false-positive rate, we can use seven
bits per key (i.e., seven hash functions).  All together, this gives
us 7 × 256Mi × 32 × 8 bits to set in this master filter to reach the
50% load factor, which works out to about 4.81 × 10¹¹, so we need
about 690 gigabits, 87 gigabytes, in this master filter and in each of
the 64 group filters.  You can verify that `math.exp(math.log(1 -
1/690e9)*(7 * 256 * 2**20 * 32 * 8))` is about 0.5 in Python.  It is
probably most practical to compute these large filters in a blocked
fashion, redundantly rehashing the whole file each time and discarding
the hash values that fall outside of the current block.

Now by probing our 87-gigabyte master filter seven times with seven
random reads, we can almost determine which of the 64 4-mebiblock
groups contain a rare hashtag: we’ll have on average 1.5 hits, one
real one and 0.5 false positives on average.  Each of these groups has
a 1.4-gigabyte filter as well — but these aren’t simply folded
versions of the original 64 filters, but rather versions built from 64
smaller subfilters which are rotated before being added together.

So in this way, with 87 gigabytes per level of the tree, we have a
five-level tree of Bloom filters which allow us to rapidly follow the
trail down to an individual 4-kilobyte block of 32 lines; individual
filters at each level cover respectively 256Mi, 4Mi, 64Ki, 1Ki, and 16
blocks, with sizes of respectively 87GB, 1.4GB, 21MB, 330KB, and 5KB
per filter.  If we must do on average 11 probes per intermediate level
(7 in the correct block and 4 or so in the false positive, which half
the time will contain no false positive) then our tree traversal
requires respectively 7, 11, 11, 11, and about 2 block reads, for a
total of 42 block reads, about a third of a second on classic spinning
rust, 12 milliseconds on the SSD I have here, or 70 μs on a PCI Optane
device.  This is between 200 and a million times faster than the same
query without the index.

XXX you don’t have to keep probing once you’ve found a cleared bit;
you’ll only on average probe a node that was a false positive in its
parent less than twice, in the case of 7 hashes 1 + ½ + ¼ + ⅛ + 1/16 +
1/32 + 1/64 = 1+63/64.  Not 3.  This means it’s 39, not 42.

The total index tree is only 440 gigabytes, sizable but less than half
as big as the original file.

For lower-selectivity hashtags, a filter probing sequence of the same
length will yield not the sole matching line but the first of many
matching lines.

An interesting thing to note is that queries for arbitrary monotonic†
Boolean combinations of hashtags still require visiting only the same
number of nodes to reach the first record of results, but probing more
hash buckets in each node, in proportion to the number of hashtags
that need to be inspected.  This makes ordering by selectivity much
less important than with traditional database index
structures — although it still helps to check the most selective
hashtags first, the speedup for a query testing N hashtags is at most
only a factor of N.

Like ordinary Bloom filters, this filter tree can be readily updated
for insertions but not for deletions or updates.

† “Monotonic” here is equivalent to “can be expressed with only AND
and OR”, excluding connectives like negation, abjunction (set
subtraction), and material implication.

Synthetic tags
--------------

A difficulty with the naïve approach is that intersections of common
tags will be extremely common at the higher levels of the tree, but
can still be rare in the leaves.  The #250-most-popular tag, for
example, will be present in 0.1% of lines, as is the #251 most
popular, but (given our hypothesis that tags are uncorrelated) the
combination is present in only one line in a million, some 268 lines
in all.  Yet the vast majority of tree nodes will have at least one
descendant line containing each of these tags; even at the last level,
almost half of them will.  The solution is to generate another few
million “synthetic tags” consisting of such combinations: all the
pairs of the most popular few hundred tags, triplets in cases where
the pairs are insufficiently rare, a few quadruplets, perhaps a
quintuplet or two.

Considering different branching factors
---------------------------------------

What if we change the branching factor?  We will start to run into
efficiency problems once we are beyond a few machine-word-sizes of
branching: 1024-way branching might be feasible, but 4096-way
branching requires operations on vectors of 4096 bits and, thus,
suffering.  Let’s consider branching factors of 256, 16, 8, and 512.

With a branching factor of 256, we need 4 levels of tree instead of 5,
but 9 hashes instead of 7 (for a 1/512 false-positive rate), so each
level takes 9/7 as much space for the same load factor, and we must
probe each node in 9 places instead of 7.  This works out to be very
nearly equal to the 64-way branching case: a factor of 36/35 on size
and slowness.

With a branching factor of 16, we need 7 levels of tree instead of 5,
but only 5 hashes instead of 7 (for a 1/32 false-positive rate), so
each level is only about 5/7 the size, and we only have to probe each
node in 5 places instead of 7, so returning the first record from a
query still requires about 44 block reads.  This is exactly equal to
the 64-way branching case.

With a branching factor of 8, we need 10 levels of tree instead of 5,
but 4 hashes instead of 7, so each level is 4/7 the size and requires
4/7 the probing.  This is slightly worse: 40/35 on both size and slowness.

With a branching factor of 512, we still need 4 levels of tree, except
that the first level only has a branching factor of 2, which is silly;
and we need 10 hashes instead of 7, so each level is 10/7 the size and
requires 10/7 the probing, for a total factor of 40/35 on both size
and slowness.  This is a little worse than the factor-256 case, but
only because it’s 4 levels instead of 3.  If the file were half as
big, it would be 30/35, which is still almost equal.

This null result for varying branching factor by a factor of 64 is not
what I expected!  What if we consider far more extreme cases?

How about using a single Bloom filter with a branching factor of
268'435'456?  Well, we probably need to crank up its precision a bit
(from the 2⁻²⁹ the above would suggest, using 29 hashes), or it will
return us half the blocks in the file as false positives.  (Above I
was assuming that 50% false positives would be fine.)  And each probe
will be reading a vector of 256 mebibits (32 mebibytes) out of the
filter, to be rotated and ANDed with the other probe results.  So we
need to do, say, 58 probes with 58 hash functions, doing 58 random
reads of 32 mebibytes each, a total of 1.8 gibibytes, sucking up a few
seconds of memory bandwidth.  But then we have a giant bitvector that
tells us exactly which couple of lines we need to look at to find the
one we’re interested in.

(XXX how much space does this use?  Maybe it's less?)

This is worse than the more reasonable cases, but only because of the
lower false-positive rate demanded and the larger bitvectors being
transferred — the raw number of probes is still almost the same!  It’s
within a factor of 2.

How about the other extreme — a branching factor of 2, false-positive
probability of 1/4, thus probing each filter twice?  Here each level
of the filter needs to be about 200 gigabits or 25 gigabytes, about
2/7 of the size previously needed, but we need 28 levels instead of 7,
so 56 probes.  This is also slightly worse than the middle-of-the-road
sizes mentioned above, but, again, by less than a factor of 2.  This
extreme, unlike the other one, is actually practical, just slightly
suboptimal.

Blocked Bloom filters
---------------------

[“Cache-, Hash-, and Space-Efficient Bloom Filters”][0] proposes
“blocked Bloom filters”, a slight variation on a normal Bloom filter
that improves locality of reference.  (This is also the paper that
proposed Golomb-coded sets.)  The idea is that, instead of scattering
the bits for the _k_ different hash functions for a single key all
over a huge Bloom filter, you use the first few bits of hash output to
pick a block of, say, 64 bytes, and then use the _k_ different hash
functions to index bits within that block.  In theory, as long as _k_
is small compared to the number of bits in the block, the performance
difference is tiny between an ordinary Bloom filter and this variant.

[0]: http://algo2.iti.kit.edu/documents/cacheefficientbloomfilters-jea.pdf "Putze, Sanders, and Singler"

This analysis mostly survives the adaptations to the Bloom-filter
algorithm described above, and it has even greater advantages in the
SSD or spinning-rust milieu.  It has no trouble with folding large
sparse filters into small dense filters.  However, it does suffer
somewhat from rotating and combining multiple filters.  In all but the
bottommost tree nodes, all the bits related to a high-frequency
hashtag will be set, forming a (say) 64-bit word of all ones.  If you
have, say, 7 such words within a single 512-bit block, they will by
themselves push that block’s load factor above 60%, before any other
keys are inserted.  So it is not _k_ that must be small compared to
the block size, but 64_k_, or whatever the branching factor is.

The obvious thing to try is to use blocks of 4096 bytes, the disk’s
transfer size, rather than 64 bytes.

Using blocked Bloom filters means that probing for a single key in a
single tree node requires only a single disk access, no matter how
large _k_ or the branching factor are, so, for example, our 5-level
tree from before can be traversed in 5 random accesses rather than 39.
This might ease the pressure towards smaller branching factors,
perhaps favoring 512-way branching — wider branching factors don’t
save you any space but they do reduce access time!

Multiattribute queries and range queries
----------------------------------------

The above is all formulated in terms of “hashtags”: each “line” has
some set of hashtags.  But what happens if we’re considering records
in a more traditional database?  You might have a record like { "lat":
-34.5384, "lon": -58.4636, "name": "Escuela Superior de Mecánica de la
Armada", "neighborhood": "Nuñez", "city": "Buenos Aires", "country":
"Argentina", "category": ["Internment camp", "Museum"] }, and you
might want to query, for example, a list of museums in Argentina.

It's straightforward to transform each name-value pair into a
“hashtag” such as “#country:Argentina” and “#category:Museum”,
generating multiple hashtags for multivalued attributes like
“category” (which would be represented as a join table in an RDBMS).
This combination of hashtags could then be used to walk the index tree
to find the records; I think this is likely to be a little faster than
doing the equivalent with ordinary database indices, because parts of
the tree that have museums but nothing in Argentina, or things in
Argentina but no museums, can be skipped over completely, while
traditional database query plans can only skip over one or the other,
(unless a multicolumn index happens to exist beginning with that pair
of columns), and must heuristically guess which index will be more
selective.  But, for reasonably common hashtags, you’ll still have to
visit most of the nodes in the tree, unless the file happens to be
sorted in a way that brings them close together.

The latitude and longitude fields, though, pose more of a problem,
because it’s unlikely that someone would query for “#lat:-34.5384”
exactly.  A much more likely scenario is retrieving latitudes in the
range of -34.52 to -34.55 and some similar range of longitudes — the
neighborhood including ESMA, Ciudad Universitaria, and the River Plate
stadium.

One way to deal with this problem is to shatter the tag into
“#lat:-0xx”, “#lat:-3x”, “#lat:-34.x”, “#lat:-34.5x”, “#lat:-34.53x”,
“#lat:-34.538x”, “#lat:-34.5384x”, and “#lat:-34.5384”.  This converts
a single attribute value into 8 separate hashtags, a number which
grows logarithmically with the number of distinct values in the file.
Then, any contiguous range query on that field can be expanded into a
query of one to eight of these tags with, at most, only about a 20%
loss of precision.