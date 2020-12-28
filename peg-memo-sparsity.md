Most PEG callsites can’t memoize usefully: either they can’t be
reached by backtracking, so they can never find a hit in the memo
table, or their result can’t be used by backtracking, so there’s no
point in saving their result in the memo table.  This should
dramatically improve the memory and even time consumption of PEG
parsers without affecting their other advantages.

The memoizability of a particular call in a PEG (an attempt to parse a
particular nonterminal at a particular position) has two aspects:
winkability — the ability to avoid doing any actual parsing by
fetching the result from the memo table; and storability — the fact
that the call’s results, if stored in the memo table, will be used by
a later winkable call.  Both of these are potentially dependent on the
entire source text, both before and after the XXX