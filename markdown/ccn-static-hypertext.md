Implementing a static hypertext system on top of a service like the
retrieve-by-hash service described in file `ccn-streams.md` is
straightforward.

Static hypertext
----------------

Each hypertext page is a file stored in the system, consisting of a
short metadata header followed by the page itself in a format such as
HTML or PDF, and it is identified by its hash.  Links to another page
include the hash `H` of the file it’s stored in.  In this way, you can
be certain that the linked page is precisely the version of the page
that the author intended; no attacker can redirect you to a different
page, not even the author herself at a later date, perhaps while being
tortured by Mossad agents.

Of course, if the attacker can trick you into looking at a page of
theirs instead, they can make a copy of an authentic page with all the
links redirected to more pages they wrote.  So all the security comes
from the security of the initial link.

This secure linking mechanism is also applicable to things like
stylesheets, image liabilities, software libraries, software
configuration files, and text transclusions.  In combination with a
deterministic archival virtual machine with immutable semantics, this
guarantees the interpretability of XXX

A single file can easily contain multiple different “pages”, as
TiddlyWiki does; the fragment-identifier mechanism of the XXX

A manifest mechanism XXX

Cache timing side channels XXX

Threat model