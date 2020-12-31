A4 paper is a rep-2 rectangle: by putting two sheets of A4 paper next
to each other, you get a larger sheet that’s the same shape as A4 if
you turn it 90°, but twice as big.  The whole A0/A1/A2 etc. system is
designed that way.  In the A-size papers, you’re never more than √2
away from the ideal size for your application.  If you add the B-size
papers, which have √2 area relation to the A-size papers, you’re never
more than ∜2 away.

I’m thinking about how to pack together boxes to make a portable
electronics lab (see [Ghettobotics Nonshopping
List](ghettobotics-nonshopping-list.md)) and it occurred to me that it
would be nice to have boxes with volumes that were powers of 2.  That
way, a small number of box designs would cover several orders of
magnitude, and I could always “buddy-system” two boxes of one size
together to fit into a space the next size up.  It’s an attempt to
minimize space fragmentation in the toolbox.

One way (maybe the only way) to make a rep-2 box in three dimensions
is to make the sides in the ratio of ∛2 to one another; for example,
100 mm × 126 mm × 159 mm.  Then the next size up is 126 mm × 159 mm ×
200 mm, for example.  These ratios are correct to within about a sixth
of a percent.

To approximate a 200-mℓ box, reasonable values are 46 mm × 58 mm ×
74 mm.  A list of mm dimensions covering a wider range, produced by
rounding and exponentiation, is [12, 15, 18, 23, 29, 37, 46, 58, 74,
93, 117, 147, 186, 234]; the resulting box volumes in mℓ are [3.24,
6.21, 12.006, 24.679, 49.358, 98.716, 197.432, 399.156, 805.194,
1599.507, 3199.014].  There’s clearly some approximation in there; you
can put together two 12×15×18 boxes into a 15×18×24 box, a millimeter
over; two 15×18×23 boxes make an 18×23×30 box, slightly over 18×23×29,
and so on.

Perhaps a more reasonable approach is to just start with some small
dimensions and double them exactly.  For example, [18, 24, 29, 36, 48,
58, 72, 96, 116, 144, 192, 232] mm gives us [12.528, 25.056, 50.112,
100.224, 200.448, 400.896, 801.792, 1603.584, 3207.168] mℓ.  I
probably only really need the first seven of those sizes, and they’re
actually closer to the ideal volumes than the ones given above,
although their ratios are a little more imperfect.

There’s no real need to have 200 mℓ be on the list, though.  I could
just look for the best triplet under about 35, which turns out to have
only about 1% error from the real cube root of 2:

    >>> min(((a, b, c) for a in range(1, 36) for b in range(1, a) for c in range(1, b)),
        key=lambda (a, b, c): max(abs((a/float(b))**3 - 2), abs((b/float(c))**3 - 2)))
    (24, 19, 15)

We can cut that 24 in half: [12, 15, 19, 24, 30, 38, 48, 60, 76, 96,
120, 152] mm, giving [3.42, 6.84, 13.68, 27.36, 54.72, 109.44, 218.88,
437.76, 875.52] mℓ.

After cutting two 12×15×19 boxes, a 15×19×24 box, a 19×24×30 box, an a
24×30×38 box out of cardboard, I conclude that probably at the
smallest sizes it makes more sense to use paper envelopes, as I am for
resistors already.  The 24×30×38 box, 27.4 mℓ, is about the smallest
one that it makes sense to make as a separate box.  And around that
size, 27×34×43 has more precise ∛2 proportions, erring by +0.4% in the
34:43 proportion and -0.05% in the 27:34 proportion.

On that basis, the dimensions should be [27, 34, 43, 54, 68, 86, 108,
136, 172, 216, 272, 344] mm and [39.474, 78.948, 157.896, 315.792,
631.584, 1263.168, 2526.336, 5052.672, 10105.344] mℓ.  I probably
won’t need anything bigger than the 1.26-ℓ box!  So the sizes are:

- 27×34×43 mm: 39.47 mℓ; call it “one bix”
- 34×43×54: 79 mℓ, two bixes
- 43×54×68: 158 mℓ, four bixes
- 54×68×86: 316 mℓ, eight bixes
- 68×86×108: 632 mℓ, 16 bixes
- 86×108×136: 1263 mℓ, 32 bixes

So with six box sizes I should be able to cover pretty much the whole
portable-lab size spectrum, with boxes always within √2 of the ideal
volume, and packing together nicely.  The 40-ℓ toolchest I was
spitballing works out to about 1013 bixes, so rounding it up to 1024
is probably more pleasant.  It won’t be bix-shaped itself.
