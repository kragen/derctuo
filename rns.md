I was wondering about residue number systems and I was thinking that
in particular bases that are close to a power of 2 are relatively easy
to reduce modulo.  Nathan Laredo came up with the list 63 62 61 59 55
53.  (\* 63 62 61 59 55 53) = 40978178010.  68 719 476 736 is the
number of possible 36-bit numbers; 40 978 178 010 is a reasonably
large fraction of that.  All the fractional bits of loss between all
those don't add up to even a whole bit of loss!

So consider, like, 1411 and 8675309.  1411 % [63 62 61 59 55 53] is
[25 47 8 54 36 33]; 8675309 reduces to [20 21 11 8 49 4].  If we
multiply these elementwise we get [ 500 987 88 432 1764 132], which
reduces to [59 57 27 19 4 26].  And *waves hands* with the Chinese
Remainder Theorem we can get the product of 1411 \* 8675309 =
12240860999, modulo 40978178010 anyway.

This works for addition and subtraction; you can only get division if
the bases are actually prime instead of relatively prime I think.

The reason this is potentially interesting is that six circuits to
multiply two six-bit numbers modulo a six-bit base are a lot cheaper
than one circuit to multiply 34-bit numbers, and have a lower path
length to boot, so you can clock them faster.  So RNSs like this get
used a lot for high-sample-rate DSP.
