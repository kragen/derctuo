External sorting on modern general-purpose computers is almost
invariably two-pass.  What if we could make it one-pass?  We kind of
can, if we cheat.  Especially with an SSD.

You might have up to 50 terabytes or so of disk attached to a modern
computer; typical performance characteristics of each disk might be 10
milliseconds random access time and 200 megabytes per second of
transfer bandwidth, and you might have a dozen or so such disks on
your machine.  Tapes have been relegated to niche applications.  So
you might reasonably want to sort up to about 20 terabytes.  But you
surely won’t have less than 4 gibibytes of RAM, probably more like 64
gibibytes.

Generally the disk bandwidth is an unavoidable bottleneck, but the
random access time of the disk (seek plus rotational latency) can
dominate it if you’re not careful.  To keep the disk bandwidth lost to
random access time below 10%, you need to transfer a sequential stream
of 9 or more bandwidth-delay products every time you access the disk.
With the figures given above, the bandwidth-delay product of each disk
is about 20 megabytes, so you need to read a chunk of 180 megabytes
after each random seek.  If you read in chunks of only 40 megabytes,
you’ll be at only ⅔ of what the disk bandwidth could hypothetically
handle; you don’t start to see big performance losses until you’re
well below that.  But if the chunks are only 2 megabytes, you’re only
able to use 9% of the disk’s potential bandwidth, and your sorting
will take 11 times longer than it should.

The standard mergesort approach is to fill RAM with input data, sort
it, and write it back out to a temporary file.  Ideally, you continue
to read in data to add to the in-memory sorted data, replacing the
data you’ve already written out, which in the worst case of the data
being backwards gains you nothing, gains you a factor of 2 in the
average case of the data being unsorted, and converts the procedure
into a single pass in the best case of the data being presorted.
Let’s take the worst case, though: 20 terabytes of data in 16-gibibyte
chunks gives us 1165 chunks.

So now we have 1165 individually-sorted temporary files of mostly 16
gibibytes each, and we want to merge them into a single output file.
So if we divide our 16 gibibytes of RAM into 1166 buffers — one for
output, the others for input — we have 14 mebibytes of buffer per file
on average.  If we wait to refill or flush each buffer until less than
a mebibyte of slack is left in it, then we can read 26 mebibytes into
the buffer, growing it from 1 mebibyte to 27 mebibytes.  This gives us
57% I/O bandwidth usage, which is not great but possibly acceptable.
If instead we have the expected 582 temporary files, we can read
55 mebibytes on each such occasion, which is 73% I/O bandwidth usage.

If we only have 4 gibibytes, though, our capability for efficient
two-pass sorting is limited to just a terabyte or two.  Two terabytes
gets divided into 466 temporary files, and so we can only allocate 9
megabytes of buffer to each file, only getting 16 megabytes per
transfer, slightly worse than the scenario above but still the right
order of magnitude.  Sorting files any larger will start to require
three-pass sorting.

What’s going on with this 20-terabyte output file, though?  We can
distribute our temporary files freely across half a dozen disks, so
our aggregate input bandwidth from those disks may exceed a gigabyte
per second, which we can then merge and write back out.  But we can’t
write a gigabyte per second to any of our disks!  We can only write at
speeds like those if we’re writing across all the disks at once.  We
have to use RAID or some kind of clever virtual filesystem that stores
a virtual file in segments on many different filesystems.  And then we
may have lost, because if those segments are large and sequential, we
may only be able to write *or read* them at 200 megabytes per second!

Cheat 1: merge on read
----------------------

So suddenly we are faced with weird existential questions like, what
is a file, anyway?  It’s not really a physical thing, but some set of
operations and behaviors that work to store our data.  What kinds of
operations does it need to support, and what kind of behaviors does it
have?  Once the sorting process’s output “file” has been created and
closed, it probably doesn’t need to support further writes; it just
needs to support reading.  What kind of reading?  Is it enough to be
able to open the “file” and get records from it one at a time in
sorted order?  Or do we also need to be able to tell where we are in
it, and seek to previously told positions in it?  Do we need to be
able to find the size of the file and seek to arbitrary byte offsets?

If it is adequate to read sequentially, telling where we are, and seek
to previously-told offsets, then we can skip the whole stage of
merging the temporary files into an output file.  Instead we can
merely decree that this collection of hundreds or thousands of
“temporary files” now constitutes the output file, which is now
divided into these parallel “chunks”.  When you go to open the results
for reading, we open *all of the chunks*, and when you read records,
we do the merge right then, on demand, in RAM.

This has some advantages!  After a single pass over the input, you can
start processing the output, doing whatever it is you want to do with
it.  And you can save bookmarks in that output and seek to them again.
But it has some disadvantages, too.  A bookmark is the current read
position in all the chunks at once!  So representing it might take 8
kilobytes.

As an alternative to seeking to a *byte offset*, you could seek to a
*key*, which would require adding extra crap to disambiguate any
possible duplicate keys.  Note that you can’t detect duplicate keys
during sorted dataset creation, only during reading, so you may need
the key to include a chunk identifier that tells which of the
thousands of chunks your desired record is in, along with a consistent
ordering across the chunks.

Seeking to a key in this way would require doing a binary search in
each chunk; if your records average 128 bytes, each expectedly-32-GiB
chunk contains 128 mebirecords, so you need to examine 27 keys in each
of (expected) 582 chunks, about 16000 operations; of these, only the
first ten in each chunk would involve random seeks, but we’re still
talking about potentially tens of seconds of waiting on spinning rust.

However, you can add a Lucene-like skip file to the dataset,
containing 512 KiB of keys sampled from each chunk and their
associated byte offsets in the chunks; if the keys are 16 bytes, you
can fit 32768 keys per chunk into the skip file, so the skip file gets
you within 1 MiB of the right place in the chunk.  The whole skip file
is only 32 MiB.  This cuts the number of seeks needed by an order of
magnitude, to only one per chunk.

Given a consistent ordering across chunks as mentioned above, it’s
possible to get all the way back to raw unidimensional byte offsets.
Say your positions in the various chunks are {3532, 832, 483, …}.  So
your byte offset in the entire dataset is the sum 3532 + 832 + 483 +
….  But seeking to such a byte offset is nontrivial: you need to guess
the right byte offset in each chunk, find the nearest record start,
read the key, take the median key, find the nearest corresponding keys
in all the other chunks (jumping some of them backwards and others
forwards), and then iterate forward or backward as necessary — or
possibly binary-search for the correct key, adding another factor of 4
or 5 to the seek-to-a-key procedures in the previous paragraphs.

This approach is pretty similar to LSM-trees.

A problem bigger than the seeking problem: opening the file requires
16 gibibytes of RAM for input buffer space!  That doesn’t leave a lot
for your application.

Cheat 2: partition on read
--------------------------

So I was thinking there might be a better idea, but this turns out to
not work very well.

Let’s consider the case of sorting a 2-terabyte file in 16 gibibytes
of RAM.  First, we take a random sample of 32768 records from the
input file to find out what the distribution of its keys is, and we
pick 1023 key values that partition the inferred distribution more or
less evenly, into 1024 partitions.  We preallocate a temporary file
for each of these partitions, a little bit bigger than we expect to
need, say 3 gigabytes, we open them all at once, and we initialize a
RAM write buffer for each temporary file.

Now, we start reading in the input file; as we read each input record,
we determine which partition it goes into, and we append it to that
partition’s buffer.  Whenever we run out of memory, we flush to disk
whichever output buffer is fullest, which has an expected size of 32
megabytes.

When we are done with this partitioning process, we have 1024
“temporary files” of 2 gigabytes each.  Each of them is unsorted
internally, but has a known size, and each of them covers a disjoint
part of the keyspace, and, importantly, is contiguous on disk — we
aren’t relying on the filesystem to magically defragment a bunch of
badly fragmented writes.

So now, to open this “output file” and start reading it sequentially
by key, our user program opens up the first partition file, reads 2
gigabytes into RAM, sorts them, closes the file, and begins iterating.
When it gets to the end of the first partition, it opens the second
partition and repeats the process.

This permits seeking to an arbitrary byte offset in the combined
output file: you subtract the sizes of partitions until subtracting
the next one would go negative, and that tells you which partition
file you need to open.  But it still takes a few seconds per seek.

So, when it works, this is an improvement over the previous technique:
you only need 2 gibibytes of input buffer memory to “read” the output
“file” instead of 16 gibibytes, and you can seek and tell with regular
byte offsets.  But seeking is still ridiculously expensive.

This approach also still requires some kind of RAID under the covers
to stripe each file across disks.

Cheat 3: square-root hybrid
---------------------------

What if we combine both of these approaches?  When “sorting” the data,
partition it into 64 equal-weight keyspace partitions, as in cheat 2.
When RAM is full, take the in-memory partition with the largest amount
of data in it — in 16 gibibytes, the average in-memory partition will
be 256 mebibytes, while the most bloated one should usually be around
512 mebibytes — and sort it, as in cheat 1, before writing it out to a
“temporary file”, let’s call it a “chunk”.  If the total dataset is 2
tebibytes, similar to the cheat-2 example, then in the end there will
be around 4096 such chunks, 64 per partition.

Now, to start reading the data “sequentially”, you open the 64 files
from the first partition and start merging them.  Doing this
efficiently requires enough buffer space for 64 files — say, 1.28
gigabytes on average to do 40-megabyte reads, but 2.56 gigabytes at
startup.

But wait!  Have we won anything?  If we didn’t partition the 2
tebibytes, we’d be writing out (in the expected case) 32-gibibyte
sorted chunks rather than ½-gibibyte chunks.  There would still only
be 64 of them if we only had 2 tebibyte of data.  So this partitioning
doesn’t buy the reader anything!

A bookmark to seek to might be represented as a partition number plus
64 32-bit file offsets.  Or, as said previously, a key plus a chunk
number.

So I think this doesn’t really help.  In fact, it hurts a little.

SSDs
----

Modern SSDs, as I understand it, can deliver 2.5 gigabytes per second
of 4-kilobyte reads, but are limited to sequential writes due to the
necessity of block erase.  This suggests that a different organization
of data in storage could work better — you can read 4-kilobyte blocks
in whatever order you want, you just want to make sure that each such
block is relatively coherent.  Between blocks, they could form a
linked list, no problem.  But of course you still have the problem
that it’s going to be pretty difficult to form a block with all the
record with keys in a given range of the keyspace before you’ve seen
all the input — the last record in the input might be in that range.

On the machine I have here, it’s more like 32 kilobytes per
transaction and only 120 megabytes per second.

So what happens if, instead of 32 megabytes per input stream, we only
need 32 kilobytes?  Generating output doesn’t get any easier — and the
trick in Cheat 2 of generating lots of output files in parallel gets a
lot harder — but reading from 1000 files to merge them, as in Cheat 1,
stops being a problem.  Suddenly you only need 16 megabytes of RAM for
your input buffers, 32 to start, rather than multiple gigabytes.
