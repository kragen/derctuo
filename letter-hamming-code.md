Watched [3Blue1Brown’s video on Hamming codes][0] recently and a couple of
thoughts occurred to me.

[0]: https://www.youtube.com/watch?v=X8jsijhllIA "Hamming codes and error correction"

First, [Hamming codes][1], like matrix parity codes, are simple enough
that you could reasonably compute them by hand, making them a
reasonable candidate for archival media.

[1]: https://en.wikipedia.org/wiki/Hamming_code

Second, you can take the same Hamming-code approach over any character
code, not just a binary code.  For example, rather than computing a
(15, 11) Hamming code by adding 4 parity bits to 11 data bits, or a
(7, 4) Hamming code by adding 3 parity bits to 4 data bits, you could
add 4 “parity” letters to 11 data letters, or 3 “parity” letters to 4
data letters, or indeed 6 “parity” letters to 57 data letters; a
variety of “parity” computations are possible but perhaps the simplest
is to use a character code assigning numbers 0 to *n*-1 to the
possible letters, and use the sum modulo *n*.  (It’s entirely
irrelevant what *n* is, but the decoder needs to know the whole code.)
This is optimized for situations in which a whole letter at a time is
damaged or lost, rather than single-bit errors.

Third, you can run either variant of the Hamming-code approach along
various axes.  If your text consists of lines of up to 57 characters,
for example, you could add 6 parity characters (or 7, for a SECDED
extended Hamming code) to each line, or you could divide it into
“pages” of 57 lines and add 6 or 7 parity *lines*, each of whose
characters would be computed over the corresponding characters in the
other lines.  This would enable the recovery of entire missing or
erroneous lines.

Fourth, you can combine this with the matrix-parity idea; for example,
you could compute an extended Hamming code both horizontally and
vertically, allowing you to correct up to one error per line, plus up
to one line with two or more errors.  This is not the most efficient
error-correcting code, but it is very simple, and enables a
substantial level of robustness.

If you were using this for archival in practice, you might want to put
the “parity” lines and columns at the beginning or end of the data,
rather than interspersing them as in the canonical Hamming-code
construction.

The ASCII character code has some disadvantages as a code to use in
this context, since its last position is an unprintable character
(DEL) and so are its first 32 positions, except arguably TAB, CR, LF,
and BEL. Also, arguably, space is unprintable; certainly it is
especially prone to OCR errors.  But if you replace the unprintable
characters with printable ones — one option would be
“␀␁␂␃␄␅␆␇␈␉␊␋␌␍␎␏␐␑␒␓␔␕␖␗␘␙␚␛␜␝␞␟␣␥”, but many others have been used
at different times, including accented letters and extra
punctuation — then you would have an error-correction code people
could very plausibly discover by hand in an archival document, for
example if microprinted.