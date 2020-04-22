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
section below, "Numpy indexing and broadcasting".

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

### Numpy indexing and broadcasting ###

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
be conformable, as if you were adding or multiplying them together,
which in a sense you are (see above about `xs + width * ys`):

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

I think the Numpy behavior of an insufficient number of indices is
disharmonious with Numpy broadcasting behavior in the following sense.
If you write a function like `lambda x: x * [3, 1, 5]`, you are in
some sense expecting that the *last* dimension of `x` will be 3 (or
possibly 1).  And if you say `x * [[2, 3, 1], [4, 1, 5]]`, you are
expecting that its last dimensions will be (2, 3) (or broadcastable to
(2, 3); for example, (1, 1), (1, 3), or (2, 1).)  As a general
principle, this means that you can write a function that works on, for
example, an RGB triplet, and then apply it to some large collection of
RGB triplets (perhaps an array of shape (320, 240, 3)), and hope that
it will serendipitously generalize to application elementwise.  And as
long as broadcasting is the only thing being applied, this works:

    >>> p = np.array([127, 63, 127])
    >>> (p * [3, 1, 5]).clip(0, 255)
    array([255,  63, 255])
    >>> p = np.array([[127, 63, 127], [121, 23, 21]])
    >>> (p * [3, 1, 5]).clip(0, 255)
    array([[255,  63, 255],
           [255,  23, 105]])

But this fails once indexing comes into play.  For example, we could
extract the green channel of `p` with `p[1]` or possibly `p[[1]]`.
But this only works in the first case above; in the second case,
instead of extracting the red channel of each pixel, it extracts all
three channels of just the first pixel.

Many other Numpy operations have the same problem.  If we want the sum
of the three components of the pixel, for example, `p.sum()` gives
them to us; but `.sum()` applies implicitly over all axes by default:

    >>> p = np.array([[127, 63, 127], [121, 23, 21]])
    >>> p.sum()
    482

And even if we specify a particular axis, the axes are counted from
the left, not the right:

    >>> p.sum(axis=1)
    array([317, 165])

To get behavior harmonious with the broadcasting behavior, we must
specify a negative axis number:

    >>> np.array([[[127, 63, 127], [121, 23, 21]]]).sum(axis=-1)
    array([[317, 165]])

Other operations have even stranger behaviors, like implicitly
flattening the array if no axis is specified:

    >>> np.array([[[127, 63, 127], [121, 23, 21]]]).cumsum()
    array([127, 190, 317, 438, 461, 482])

If we want to form a sum table of the color channel of each pixel, we
can specify axis=-2:

    >>> np.array([[[127, 63, 127], [121, 23, 21]]]).cumsum(axis=-2)
    array([[[127,  63, 127],
            [248,  86, 148]]])

For better or worse, this fails on a single pixel:

    >>> np.array([127, 63, 127]).cumsum(axis=-2)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ValueError: axis(=-2) out of bounds

This desideratum of supporting serendipitous vectorization by implicit
rank polymorphism probably requires redesigning the "reshape" operator
mentioned earlier so that it won't accidentally break this
vectorization.

### Octave indexing and broadcasting ###

Octave has totally different behavior, implicitly flattening for
indexing with a single index:

    octave:24> y = ['how'; 'dly'];
    octave:9> y([1 2 1])
    ans = hdh

You do, however, get Numpy-like behavior when you supply both indices:

    octave:13> y(:, [1 2 1])
    ans =

    hoh
    dld

    octave:14> y([1 2 1], :)
    ans =

    how
    dly
    how

    octave:32> y(:, [3 3 2 3])
    ans =

    wwow
    yyly

Moreover, for Octave, "all objects have a minimum of two dimensions",
so indexing once into a vector is really indexing into the second
dimension of a matrix:

    octave:15> z = 'waltz'
    z = waltz
    octave:17> z([1 2 1])
    ans = waw
    octave:18> z([1 2 1], :)
    error: A(I,J): row index out of bounds; value 2 out of bound 1
    octave:18> z(:, [1 2 1])
    ans = waw
    octave:19> z([1 1 1], [1 2 1])
    ans =

    waw
    waw
    waw

Note that this last result shows that Octave is not broadcasting the
indexes together the way Numpy does.

You can extend the matrix to an arbitrary number of dimensions,
treating this 1×5 matrix as a 1×5×1 array, or 1×5×1×1×...:

    octave:34> size(z([1 1], [1 2 1], [1 1 1], [1 1], [1 1]))
    ans =

       2   3   3   2   2

    octave:22> z([1 1], [1 2 1], [1 1 1])
    ans =

    ans(:,:,1) =

    waw
    waw

    ans(:,:,2) =

    waw
    waw

    ans(:,:,3) =

    waw
    waw


Note that the output here shows that Octave's index order is closer to
Fortran order than to C order: the rightmost indices vary most slowly,
not most quickly.  This is consistent if you read down the columns of
each displayed matrix, but if you insist on reading each row from left
to right before proceeding to the next one, as if you were reading
English rather than Chinese, then the first two dimensions are an
exception.  This is even clearer looking at the behavior of `reshape`:

    octave:54> w = reshape(1:24, [2 3 4])
    w =

    ans(:,:,1) =

       1   3   5
       2   4   6

    ans(:,:,2) =

        7    9   11
        8   10   12

    ans(:,:,3) =

       13   15   17
       14   16   18

    ans(:,:,4) =

       19   21   23
       20   22   24


As with Numpy, you can get an output with a more complicated shape by
indexing *once* with a more complicated shape:

    octave:27> z([1 2 4; 4 1 2])
    ans =

    wat
    twa

However, this doesn't work if you're indexing multiple dimensions, in
which case instead of implicitly flattening the thing you're indexing
into, as above, you implicitly flatten each index, in Fortran order,
giving an INTERCAL-like flavor in this case:

    octave:41> z([1 1], [3 1 4; 1 5 1])
    ans =

    lwwztw
    lwwztw

    octave:36> size(z([1 1], [1 2 1], [1 1 1; 1 1 1]))
    ans =

       2   3   6

I thought that maybe Octave's (or rather MATLAB's) implicit flattening
is where Numpy gets its implicit flattening, but in fact Octave
*doesn't* implicitly flatten in `sum`, `prod`, `max`, and `cumsum`,
which implicitly apply along the fastest-varying axis, which happens
to be the leftmost:

    octave:48> sum([3 1 4; 1 5 9])
    ans =

        4    6   13

    octave:49> max([3 1 4; 1 5 9])
    ans =

       3   5   9

    octave:50> prod([3 1 4; 1 5 9])
    ans =

        3    5   36

    octave:51> cumsum([3 1 4; 1 5 9])
    ans =

        3    1    4
        4    6   13

However, these don't decrease the dimensionality of the result; they
just shrink one of its dimensions to size 1:

    octave:58> size(w)
    ans =

       2   3   4

    octave:59> size(sum(w))
    ans =

       1   3   4

You can specify a different axis for the aggregation, as in Numpy:

    octave:73> size(sum(w, 2))
    ans =

       2   1   4

What about broadcasting?  Unlike in Numpy, it's consistent with `sum`
and indexing, in that it left-aligns the dimensions rather than
right-aligning them, although this is somewhat confusing if you forget
that it considers an ordinary row vector to be 1×N:

    octave:60> w + [100 1000]
    error: operator +: nonconformant arguments (op1 is 2x3x4, op2 is 1x2)
    octave:60> w + [100 1000 10000]
    warning: operator +: automatic broadcasting operation applied
    ans =

    ans(:,:,1) =

         101    1003   10005
         102    1004   10006
    ...

Still, though, there is no possibility of getting serendipitous
multiplicity generalization in Octave on a function that uses
indexing; indexing with too few indices will flatten the omitted
trailing dimensions down into the last dimension.  This is a
generalization of what happens when you index with just a single
dimension:

    octave:69> w(:, 10)
    ans =

       19
       20

    octave:70> w(:, 10, :)
    error: A(I,J,...): index to dimension 2 out of bounds; value 10 out of bound 3
    octave:72> w(:, 1, 4)
    ans =

       19
       20

### R indexing and broadcasting ###

R almost completely lacks the kind of rank-polymorphism I'm looking
for.

R, like Octave, uses Fortran order (and 1-based indexing), and
implicitly flattens when you index a matrix with just one index:

    > y <- c('h', 'd', 'o', 'l', 'w', 'y')
    > dim(y) <- c(2,3)
    > y
         [,1] [,2] [,3]
    [1,] "h"  "o"  "w" 
    [2,] "d"  "l"  "y" 
    > y[1]
    [1] "h"
    > y[1,]
    [1] "h" "o" "w"
    > y[,1]
    [1] "h" "d"
    > y[c(1,2,1),]
         [,1] [,2] [,3]
    [1,] "h"  "o"  "w" 
    [2,] "d"  "l"  "y" 
    [3,] "h"  "o"  "w" 

Unlike in Octave, this really is a special case for a single index;
you can index a 2×2×2 array with one index or three, but not two:

    > j <- c(1, 2, 2, 1, 1, 2, 2, 1)
    > dim(j) <- c(2, 2, 2)
    > j
    , , 1

         [,1] [,2]
    [1,]    1    2
    [2,]    2    1

    , , 2

         [,1] [,2]
    [1,]    1    2
    [2,]    2    1
    > j[4]
    [1] 1
    > j[2, ]
    Error in j[2, ] : incorrect number of dimensions
    > j[2, 1]
    Error in j[2, 1] : incorrect number of dimensions
    > j[2, 1, 2]
    [1] 2

This thing where the structure of a complicated index is replicated in
the output doesn't seem to be present; indexing by `j` above just
flattens `j` into a vector of indices:

    > y[j]
    [1] "h" "d" "d" "h" "h" "d" "d" "h"
    > y[j,]
         [,1] [,2] [,3]
    [1,] "h"  "o"  "w" 
    [2,] "d"  "l"  "y" 
    [3,] "d"  "l"  "y" 
    [4,] "h"  "o"  "w" 
    [5,] "h"  "o"  "w" 
    [6,] "d"  "l"  "y" 
    [7,] "d"  "l"  "y" 
    [8,] "h"  "o"  "w" 

Multiple indices are not broadcast together, as in Numpy, but instead
give a Cartesian product, as in Octave:

    > y[j,j]
         [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8]
    [1,] "h"  "o"  "o"  "h"  "h"  "o"  "o"  "h" 
    [2,] "d"  "l"  "l"  "d"  "d"  "l"  "l"  "d" 
    [3,] "d"  "l"  "l"  "d"  "d"  "l"  "l"  "d" 
    [4,] "h"  "o"  "o"  "h"  "h"  "o"  "o"  "h" 
    [5,] "h"  "o"  "o"  "h"  "h"  "o"  "o"  "h" 
    [6,] "d"  "l"  "l"  "d"  "d"  "l"  "l"  "d" 
    [7,] "d"  "l"  "l"  "d"  "d"  "l"  "l"  "d" 
    [8,] "h"  "o"  "o"  "h"  "h"  "o"  "o"  "h" 

`sum` and `cumsum` flatten by default, as in Numpy:

    > sum(p)
    [1] 482
    > cumsum(p)
    [1] 127 190 317 438 461 482

There is no optional "axis" argument, as in Numpy and Octave; instead
there are some special-case functions:

    > colSums(p)
    [1] 317 165
    > rowSums(p)
    [1] 248  86 148

Broadcasting left-aligns dimensions, as in Octave, but seems to be
limited to scalars and vectors, and has truly bizarre behavior:

    > p <- c(127, 63, 127, 121, 23, 21)
    > dim(p) <- c(3, 2)
    > p
         [,1] [,2]
    [1,]  127  121
    [2,]   63   23
    [3,]  127   21
    > p * c(3, 1, 5)
         [,1] [,2]
    [1,]  381  363
    [2,]   63   23
    [3,]  635  105

So far, so reasonable.  But look at *this*:

    > p + c(1, 2, 3, 4, 5, 6)
         [,1] [,2]
    [1,]  128  125
    [2,]   65   28
    [3,]  130   27

The vector got implicitly reshaped!  Weirder still, given a 2-vector,
it gets broadcast down columns instead of across rows --- or does it?

    > p + c(1, 2)
         [,1] [,2]
    [1,]  128  123
    [2,]   65   24
    [3,]  128   23

If the matrix is square so that the vector could be broadcast either
horizontally *or* vertically, it gets broadcast horizontally:

    > p[,c(1, 2, 1)] + c(1000, 10000, 100000)
           [,1]   [,2]   [,3]
    [1,]   1127   1121   1127
    [2,]  10063  10023  10063
    [3,] 100127 100021 100127
    > p + c(100, 1000, 10000, 5)
          [,1] [,2]
    [1,]   227  126
    [2,]  1063  123
    [3,] 10127 1021
    Warning message:
    In p + c(100, 1000, 10000, 5) :
      longer object length is not a multiple of shorter object length

The horrifying truth is that it's just replicating the vector down the
columns to "broadcast" it --- it wasn't applying it to columns after
all!  `p[,1] + 1` is c(128, 64, 128), not c(128, 65, 128) as given
above.  But even when *it doesn't fit*, you only get a *warning*.

At the other extreme, suppose you want to add a 2×2 matrix to our
2×2×2 `j` above.  Nothing doing!

    > i <- c(10, 100, 1000, 10000)
    > dim(i) <- c(2, 2)
    > i + j
    Error in i + j : non-conformable arrays

Given the above, you'd think we could do that if `i` is just a vector,
but no, apparently that implicit flattened replication is just for
matrices:

    > dim(i) <- 4
    > i + j
    Error in i + j : non-conformable arrays
    > dim(j)
    [1] 2 2 2
    > dim(i)
    [1] 4

We can still add a 2-vector to `j`, and it broadcasts horizontally and
depthwise as expected.

    > j + c(100, 1000)
    , , 1

         [,1] [,2]
    [1,]  101  102
    [2,] 1002 1001

    , , 2

         [,1] [,2]
    [1,]  101  102
    [2,] 1002 1001

And a 4-vector broadcasts depthwise:

    > j + c(10, 100, 1000, 10000)
    , , 1

         [,1]  [,2]
    [1,]   11  1002
    [2,]  102 10001

    , , 2

         [,1]  [,2]
    [1,]   11  1002
    [2,]  102 10001

But we cannot add a 2×2-element array, or a 2-element array, to `j`,
because in R, vectors and arrays are different classes of things that
just happen to look exactly the same most of the time:

    > k <- c(10, 100)
    > dim(k) <- 2
    > j + k
    Error in j + k : non-conformable arrays
    > k
    [1]  10 100
    > c(10, 100)
    [1]  10 100
    > class(c(10, 100))
    [1] "numeric"
    > class(k)
    [1] "array"
    > dim(c(10, 100))
    NULL
    > dim(k)
    [1] 2

As far as I can tell, for arrays to be conformable, they must have
exactly the same shape, with no broadcasting.

Program serialization as strings of bytes: let's use text!
----------------------------------------------------------

The usual way to represent programs for a virtual machine is as some
kind of binary bytecode.  This is relatively fast to load, but it
requires at least some kind of assembler to construct it from a
human-readable format (if not a compiler from a higher-level language)
and probably some kind of disassembler as well to help with debugging.
(If the virtual machine is producing the wrong results on some
program, you need some way to puzzle out what the program is telling
it to do, in order to figure out whether the bug is in the program or
the VM.)

I think that for this purpose it might be a reasonable alternative for
the virtual machine itself to parse a simple textual syntax that is
sufficiently friendly to write directly by hand and read with a text
file viewer, even if it lacks some of the amenities one might want in
a programming language.  For example, you might use a syntax similar
to PostScript, or FORTH, or Lisp.
