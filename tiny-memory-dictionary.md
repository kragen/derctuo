Instead of using a hashtable you can use binary search on an array,
which wastes no space and permits ordered traversal.  This is slow to
insert into when the array gets big.  If you only have 64K of RAM it
doesn't get *that* slow; if you have 16384 entries then the worst case
is moving 16384 entries, and building the whole 16384-entry thing
incrementally takes an expected 67 million entries moved of work.

But it gets a lot faster if you have a side file with, say, 128
entries, which you maintain sorted and then merge into the main array
whenever it gets full.  Filling memory that way requires doing an
expected 2k entries moved to build each side file, and then iterating
over the whole array to do the merge, which takes on average copying
8k entries, so 10k entries moved in all; doing this the requisite 128
times requires 1.3 million entries moved to fill RAM.  This is
slightly more complex and wastes 128 entries more RAM than the
single-array approach but is 50 times faster.  Also instead of
requiring worst-case 14 probes to find an entry it requires 21.  You
need slack space for 256 entries because merging requires you to copy
one of your merge inputs.  (Supposedly there's an in-place mergesort
but I don't understand it.)

The standard log-structured merge tree approach where you have a
1-entry side file merged into a 2-entry side file merged into a
4-entry side file, etc., starts to run into problems when you don't
have more RAM to merge in.  A possible solution to this problem is to
use quicksort or introsort instead of doing large merges, at the cost
of some extra complexity and slowness; merging 16384 entries takes 1
step per entry, but quicksorting them takes expected about 20 steps
per entry, and heapsort is even slower.

A sort of library-sort approach might help: divide your sorted
dictionary into hashtable-like buckets, each big enough to hold, say,
8 items, binary-search the buckets, and then linear-search the
selected bucket.  This allows some slack space within each bucket, so
inserting an entry is usually very fast; when a bucket fills up, you
can sort the entire array (sorting the slack-space items to the end,
say; perhaps first compact, then do an insertion sort) and
redistribute evenly into either the same number of buckets, thus
redistributing the slack space, or a larger number, dramatically
increasing it.  There's a tradeoff between wasted slack space and
frequency of resizing, a tradeoff which eases with larger bucket sizes
at the cost of search time.
