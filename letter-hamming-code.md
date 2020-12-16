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

TeX OT1
-------

One particularly handy 7-bit all-printable encoding for text is [TeX’s
OT1 font encoding][2], whose translation into Unicode Wikipedia gives
as follows, if I haven't screwed it up:

    Γ U+0393
    Δ U+0394
    Θ U+0398
    Λ U+039B
    Ξ U+039E
    Π U+03A0
    Σ U+03A3
    Υ U+03A5
    Φ U+03A6
    Ψ U+03A8
    Ω U+03A9
    ﬀ U+FB00
    ﬁ U+FB01
    ﬂ U+FB02
    ﬃ U+FB03
    ﬄ U+FB04
    ı U+0131
    ȷ U+0237
    ` U+0060
    ´ U+00B4
    ˇ U+02C7
    ˘ U+02D8
    ˉ U+02C9
    ˚ U+02DA
    ¸ U+00B8
    ß U+00DF
    æ U+00E6
    œ U+0153
    ø U+00F8
    Æ U+00C6
    Œ U+0152
    Ø U+00D8
    ̷ U+0337
    ! U+0021
    ” U+201D
    # U+0023
    $ U+0024
    % U+0025
    & U+0026
    ’ U+2019
    ( U+0028
    ) U+0029
    * U+002A
    + U+002B
    , U+002C
    - U+002D
    . U+002E
    / U+002F
    0 U+0030
    1 U+0031
    2 U+0032
    3 U+0033
    4 U+0034
    5 U+0035
    6 U+0036
    7 U+0037
    8 U+0038
    9 U+0039
    : U+003A
    ; U+003B
    ¡ U+00A1
    = U+003D
    ¿ U+00BF
    ? U+003F
    @ U+0040
    A U+0041
    B U+0042
    C U+0043
    D U+0044
    E U+0045
    F U+0046
    G U+0047
    H U+0048
    I U+0049
    J U+004A
    K U+004B
    L U+004C
    M U+004D
    N U+004E
    O U+004F
    P U+0050
    Q U+0051
    R U+0052
    S U+0053
    T U+0054
    U U+0055
    V U+0056
    W U+0057
    X U+0058
    Y U+0059
    Z U+005A
    [ U+005B
    “ U+201C
    ] U+005D
    ˆ U+02C6
    ˙ U+02D9
    ‘ U+2018
    a U+0061
    b U+0062
    c U+0063
    d U+0064
    e U+0065
    f U+0066
    g U+0067
    h U+0068
    i U+0069
    j U+006A
    k U+006B
    l U+006C
    m U+006D
    n U+006E
    o U+006F
    p U+0070
    q U+0071
    r U+0072
    s U+0073
    t U+0074
    u U+0075
    v U+0076
    w U+0077
    x U+0078
    y U+0079
    z U+007A
    – U+2013
    — U+2014
    ˝ U+02DD
    ˜ U+02DC
    ¨ U+00A8

[2]: https://en.wikipedia.org/wiki/OT1_encoding

This is intentionally missing some mathematical symbols, namely `<`,
`>`, `{`, `|`, and `}`, as well as some others rarely used in text
like `\\` and `_`.  TeX generally sets mathematical symbols in a
different font (which makes it a bit strange to include some Greek
letters but not enough to actually write Greek), and can stack glyphs
one on top of the other, so separate codepoints for letters like áéòč
are not needed.

