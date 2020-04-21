A big part of the mission for Derctuo is to make computational
experiments reproducible, both by removing nondeterministic choices
from the implementation and by minimizing environmental dependencies.
Can we reconcile this with efficiency by implementing a vector virtual
machine?

The background of the problem
-----------------------------

Computational experiments are more compelling when they can use a
larger fraction of the power of your computer, and typical interpreted
languages waste on the order of 97% of your computer's computational
power.  Now that everybody's computer is massively parallel with
4-wide SIMD operations and 4-32 cores, even single-threaded
nonvectorized C wastes on the order of 97% of your computer's
computational power; typical interpreted languages like Python or PHP
thus waste 99.9% of its power.  (And that's assuming you don't have a
GPU, which can easily push that to 99.99%.)  In effect, using
languages implemented in this way costs you three orders of magnitude
of performance, pushing you 15 years into the past, to 02005 or so ---
a performance price that implies a progressively longer timespan as we
get further and further out of the shadow of Moore's Law.

Simple untyped virtual machines like Chifir, Dontmove, Wirth's RISC,
or the Cult of the Bound Variable's Universal Machine suffer a similar
performance hit: not only are they single-threaded, but also, like
Forth, they typically spend about 5× as much work on instruction
dispatch as they do on useful computation.  This is less than all the
suffering induced by all of Python's type-checking and
bounds-checking, but it's still painful.  This offers implementors an
unappealing tradeoff: either they can accept painfully limited
performance, or they can add a lot of complexity to their
implementation in the form of clever optimizations to try to reduce
the performance price, at the potential cost of breaking correctness.

One of the great historical advantages of languages like Octave, R,
Numpy, Yann LeCun's Lush, and APL is that even a fairly
straightforward interpreter is capable of achieving reasonable speeds,
because the inner loops are not interpreted --- they happen within
primitives of the language like a+b, +/a, or \*\\a.  This is somewhat
less true nowadays that our cache hierarchies are so deep and data
locality is so important; while straightforward Python code usually
runs around 3% of the speed of C, locality effects usually limit
straightforward Numpy code to around 20% of the speed of C (comparable
to interpreted Forth or something like Chifir), and optimized Numpy
code usually runs around 33% of the speed of C.

Nowadays, a potential additional interesting advantage is that
programs in such languages expose data parallelism in a way that a
relatively straightforward interpreter could potentially exploit, if
the overhead for moving data between threads or processes in the host
system is not too great.  Maybe you could use 20% of your whole
machine instead of 20% of one core.

You could easily imagine splitting a computation like this one across
cores by either row or column, although perhaps not until it's much
larger:

    >>> np.arange(12).reshape((3, 4)) * (np.arange(3) + 4).reshape((3, 1))
    array([[ 0,  4,  8, 12],
           [20, 25, 30, 35],
           [48, 54, 60, 66]])

Implementation limits
---------------------

As I've said elsewhere, Lorie's UVC falls down on compatibility
grounds when it refuses to apply limits to things like register bit
sizes.  Not putting a limit in the specification doesn't mean that
implementations won't have limits; it just means that every
independent implementation will have different limits, so the
specification is insufficient for compatibility.

In particular, in this case, I think there should be maximum sizes on
all arrays and indices, probably 2\*\*32.

Determinism via non-mutation
----------------------------

Still, it seems likely that implementors still face an unappealing
performance-correctness tradeoff, in a different way: they will want
to perform loop fusion to avoid useless traffic to main memory, but
for some virtual-machine designs, it would be easy for such loop
fusion to produce different results in some circumstances,
specifically when the output aliases one or more of the inputs.  Numpy
sometimes does produce unexpected results when the output of an
operation aliases its input --- by itself, that doesn't necessarily
violate the desideratum of reproducibility, but you would have to nail
down precisely what results are required, and it would be easy for
loop-fusion optimizations, among others, to accidentally break those
results.

Still, these problems only arise if data is mutable.  Numpy data is
mutable, but, for example, APL data is purely immutable, at a logical
level.  You can say R[3] <- 4 in APL, and after that R[3] is indeed 4,
but any aliases to R are not affected, though I think typically
implementations avoid making a physical copy when possible.  If this
immutability were an inherent part of the virtual machine, the
opportunities for such nondeterminism would be vastly rarer.  There's
still the possibility for an implementor to use reference counts to
conditionally do in-place updates in order to reduce memory traffic
(or memory usage) and botch it.

So, if the virtual machine definition treats arbitrary-sized arrays
(up to the maximum) as if they were immutable atomic numbers, it
should mostly steer clear of this kind of nondeterminism.  This also
suggests treating the machine's memory as a storage not for bytes but
for arrays, like a Python module is a storage for Python objects.

Toward an instruction set design?
---------------------------------

Simple scalar virtual machines like those mentioned above commonly
have 16 or so instruction opcodes: four or five arithmetic operations,
one to four bitwise operations, some comparisons and conditional
jumps, procedure call and return, and maybe load, store, load literal,
and maybe some kind of I/O operations (both Chifir and the CBV UM have
"read keyboard" instructions).  By contrast, len(dir(numpy)) is 587,
and that doesn't even include the 163 methods on Numpy arrays, though
some are duplicates.  Even old APLs normally have on the order of 60
built-in functions, without counting the results of operators like ×.+
or +/.  Can this be reduced down to something reasonable?  Maybe 32
opcodes or 64, not 700.

(Of course, many of these items in Numpy are non-fundamental
operations like `average`, `bartlett`, and `fft`.)

Lush is unusual among array languages in that it exposes some inner
machinery that is usually kept hidden; a Lush "matrix" or "tensor"
consists of a "storage" and an "index".  The storage is a
one-dimensional array of some homogeneous atomic element type, and the
storage is realized as a base pointer, a length, an element type, and
flags indicating writability and memory-mappedness; the index contains
a pointer to a storage, a start offset into that storage, a number of
dimensions, and an upper bound and an address increment (possibly
zero!) for each dimension.  Exposing something like this in the
instruction set might save the virtual machine a large number of
index-manipulation operations: reshape, matrix transposition, matrix
diagonal extraction, ravel, sliding windows (by having two dimensions
with the same stride), shape extraction, take, drop, generating arrays
filled with a constant, and so on.

One way to supply this facility would be to have the following:

- a shape(array) operation to extract a possibly-empty vector of
  dimension bounds;
- a reshape(array, shape, strides) operation which creates a new array
  of the given shape from the raveled elements of array, using the
  stride vector strides;
- and a drop(N, array) operation which drops the first N items of
  array.

If the array being reshaped or dropped is already irregular, we might
have to copy it, and it isn't clear what drop() should do on
non-one-dimensional arrays.

Could we get by with just one-dimensional vectors and slicing
operations?  The Python expression s[3:10:2] gives us a list of items
3, 5, 7, and 9; a similar instruction could take a vector, a start, a
*count* rather than an end, and a stride, which could be zero.  Even
this could be decomposed into an index-generation instruction that
produces the vector [3 5 7 9] (just as the Octave expression 3:2:10
does) and an indexing instruction.  Is that kind of thing adequate to
express my example Numpy expression from earlier looplessly in terms
of one-dimensional arrays?

    >>> np.arange(12).reshape((3, 4)) * (np.arange(3) + 4).reshape((3, 1))
    array([[ 0,  4,  8, 12],
           [20, 25, 30, 35],
           [48, 54, 60, 66]])

I don't think so.  The column vector on the right [[4] [5] [6]] is in
effect being transformed into [[4 4 4 4] [5 5 5 5] [6 6 6 6]], which
you could get by indexing it with [[0 0 0 0] [1 1 1 1] [2 2 2 2]] in a
gathering operation.  (You can literally do this in Numpy:
(`np.arange(3) + 4)[[[[0, 0, 0, 0], [1, 1, 1, 1], [2, 2, 2, 2]]]]`.)

Another tricky problem is how to compile something like `lambda x:
np.arange(12).reshape((3, 4)) * x`.  You could apply this to an x like
the 3-column above, in which case you need to broadcast each of its
elements across a row; but you could also apply it to an x such as
np.arange(4), in which case you need to broadcast each of its elements
across a column, or to a scalar, or to a 3×4 matrix, or for that
matter to a 2×3×4 array like `np.arange(24).reshape((2, 3, 4))`.  If
you're going to insert a sequence of virtual machine instructions to
distinguish among cases like these before every multiplication
operation, you are going to incur enough interpretation overhead that
actual vector programming languages will not run well on your vector
virtual machine; if you want to have this broadcasting logic at all,
it is probably better to push it down into the definition of the
virtual-machine operation; and of course that would require the VM to
see the values as N-dimensional arrays, not just vectors.

Operations on boolean arrays in APL are traditionally unified with, I
think, gcd and lcm, but it seems to me more reasonable to unify them
with pairwise max and min.  In some sense, an N-bit integer in a
computer is an N-item boolean vector, and this is an efficient way to
represent boolean arrays; since we probably need pairwise max and min
in any case, it might be best to specify two operations to translate
back and forth between boolean arrays and arrays of N-bit integers,
rather than specifying bitwise AND, OR, and NOT operations.  An
efficient implementation can do this without copying.

There's an indexing operation.  Indexing a vector by an array index
performs a gather, producing a result with the same shape as the
index.  It isn't clear what should happen when you index a
multidimensional array by anything other than some scalars; see the
section below, "Numpy indexing".

There's an index update operation.  It produces a new array that is
mostly the same as an old array, but has some indices replaced.  For
things like painting pixels in a framebuffer, it seems like it might
be important to support things like `pix[xs, ys] = red`, although I
guess you could reshape the framebuffer into a vector first and index
it with `xs + ys * width`.

(The reshaping operation mentioned earlier could be seen as indexing
an array with one or more indices with special characteristics, like
"slice objects" or "range objects"; would it make sense to just
provide an index generation operation and leave the reshaping to the
indexing operation?  A simple implementation could omit optimizing the
special case, and the extra orthogonality would allow it to be used
with index update as well, maybe.  But in some cases not optimizing
that special case results in quadratic or worse memory blowup.)

What about reductions and scans?  Like indexing of multidimensional
arrays, these need some axis to run along, but they also need a binary
operator.  You could use the reshape operation to reorganize the axes
so that the desired axis comes first, or maybe last.

### Numpy indexing ###

There are several possible ways to index multidimensional arrays in
Numpy:

    >>> y                  # shape (2, 3)
    array([['h', 'o', 'w'],
           ['d', 'l', 'y']], 
          dtype='|S1')
    >>> y[[0, 1, 0]]       # Indexing by default is on the first dimension
    array([['h', 'o', 'w'],
           ['d', 'l', 'y'],
           ['h', 'o', 'w']], 
          dtype='|S1')
    >>> y[[0, 1, 0], ...]  # equivalent
    array([['h', 'o', 'w'],
           ['d', 'l', 'y'],
           ['h', 'o', 'w']], 
          dtype='|S1')

Indexing by a complicated thing replaces the indexed dimension with
its shape:

    >>> y[[[[[[0, 1, 0]]]]]]
    array([[[[['h', 'o', 'w'],
              ['d', 'l', 'y'],
              ['h', 'o', 'w']]]]], 
          dtype='|S1')
    >>> y[[[[[[0, 1, 0]]]]]].shape
    (1, 1, 1, 3, 3)
    >>> y[..., [2, 2, 1, 2]]      # Here we index on the other dimension
    array([['w', 'w', 'o', 'w'],
           ['y', 'y', 'l', 'y']], 
          dtype='|S1')
 
If you're indexing along multiple dimensions at once, the indices must
be conformable, as if you were adding or multiplying them together:

   >>> y[[0, 1, 0], [2, 2, 1, 2]]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    IndexError: shape mismatch: indexing arrays could not be broadcast together with shapes (3,) (4,) 
    >>> y[[0, 1, 0, 0], [2, 2, 1, 2]]
    array(['w', 'y', 'o', 'w'], 
          dtype='|S1')

This can lead to some ambiguity about where the dimensions taken from
the index should be merged into the dimensions of the thing being
indexed; Numpy seems to prefer the earliest candidate position:

    >>> a
    array([[[0, 1, 2, 3],
            [4, 5, 6, 0],
            [1, 2, 3, 4]],

           [[5, 6, 0, 1],
            [2, 3, 4, 5],
            [6, 0, 1, 2]]])
    >>> a.shape
    (2, 3, 4)
    >>> a[[[1]], ..., [[1]]]
    array([[[6, 3, 0]]])
    >>> _.shape
    (1, 1, 3)

This can be quite surprising in the presence of broadcasting:

    >>> a[[[1], [1], [1], [1], [1]], ..., [[1, 1, 1, 1, 1, 1]]].shape
    (5, 6, 3)
    >>> a[[[1], [1], [1], [1], [1]], ..., [1, 1, 1, 1, 1, 1]].shape
    (5, 6, 3)
    >>> a[...,                       ..., [[1, 1, 1, 1, 1, 1]]].shape
    (2, 3, 1, 6)
