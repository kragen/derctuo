Most PEG callsites can't memoize usefully: either they can't be
reached by backtracking, so they can never find a hit in the memo
table, or their result can’t be used by backtracking, so there’s no
point in saving their result in the memo table.  This should
dramatically improve the memory and even time consumption of PEG
parsers without affecting their other advantages.
