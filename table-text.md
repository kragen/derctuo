“Plain ASCII text files” traditionally means files that could be
interpreted directly by an ASR-33, with a CR LF sequence at the end of
each line and a fixed-width font.  Unix simplified this by eliminating
the CRs, and CRT terminals simplified it by eliminating overstrikes.
Nowadays we’ve usually extended this to UTF-8 Unicode text and
sometimes ANSI color and other SGR escape sequences, and for a program
with a terminal interface, other escape codes.

But this is terribly limiting.  Usually we have only a single font
size (though the VT100 did support double-width and double-height
characters, most modern terminal emulators don’t support them, and
that’s not much of an improvement really) and the font still has to be
fixed-width.  Otherwise our carefully vertically aligned tables and
ASCII art will get mangled by the unknowable font metrics of the
user’s viewer.

Formatting those fixed-width-font tables and ASCII art isn’t easy,
either.  It takes substantially more code than just spewing out some
strings.

Markdown, or some variant thereof, might be a reasonable choice; you
could write a Markdown or Markdown-variant terminal program.
CommonMark doesn’t support tables, but [pandoc, GitHub-flavored
Markdown, and PHP Markdown Extra do][0].

[0]: https://gist.github.com/srawlins/ad5ef4d153bc0fc223e1

It occurs to me that a slight variation on the ordinary Unix
interpretation of ASCII or UTF-8 text could work, as well.  Suppose
you consider a text (any text) to consist of a sequence of tables,
where the rows are separated by LF (^J), and the tables are separated
by blank lines consisting of two consecutive LFs (^J^J).  Thus
ordinary paragraphs are single-column tables.  Then, instead of
treating TAB (^I) to instruct a terminal to move the cursor to the
next tab stop, treat it as instructing the terminal to move the cursor
to the next *column*; enough space for each column is allocated to
hold its contents, which means that text in subsequent rows of the
table can expand them, moving previously displayed text to the right.

This form of output is clearly very easy to produce in a program, and
it can be reasonably copied and pasted between programs.  In fact lots
of programs already accept a table in this format as input or produce
it as output, under the name TSV, “tab-separated values”.

However, no self-respecting programmer would rest content without
adding recursion.  So if we use the characters ^R and ^T (DC2 and DC4)
to begin and end *nested* tables (or blank-line separated sequences of
tables), we gain new and exciting abilities:

1. We can put a paragraph of text in a table cell, as long as we wrap
   it beforehand, just by beginning it with ^R and ending it with ^T.
2. We can put a header across the top of a whole table by beginning
   the table with ^R and ending it with ^T, so that the header isn’t
   really part of the table.
3. If the vertical layout of the table is well-defined, we can split a
   table into vertical slices with their own headers by putting each
   of the vertical slices in its own table cell.
4. In general we can do Tk-style packing layout or TeX-style vbox/hbox
   layout by nesting “tables” each consisting of just one row or just
   one column.

A document in this format isn’t merely *readable*, it’s also
*editable* at the character level, although deleting a ^T or inserting
a ^R may have surprising and exciting results.  Incremental relayout
is vastly easier than with the CSS box model.  And proportional fonts
don’t inconvenience the table layout in the slightest.

Still, I feel like this is maybe more of a 1995 protocol or format
design than a 2025 design.
