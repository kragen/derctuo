In Forth and some other contexts, there's a `*/` operation which
multiplies by a ratio in integer arithmetic, avoiding overflow
typically by using a double-precision intermediate product.  So even
if you're using a 16-bit Forth, `23082 7 8 */` should give you 20196,
which only differs from the correct answer 20196¾ by ¾, rather than
3812, which is wildly wrong but what you would get if you truncated to
16 bits after the multiplication.

It occurred to me to wonder if you can do the division and the
multiplication at the same time, if you're doing this in hardware,
thus overlapping the multiplication with the division.  Division can
generate a quotient one bit at a time, and those quotient bits can be
used to control a garden-variety shift-and-add multiplier.
