So I just looked at [Secure Scuttlebutt](secure-scuttlebutt.md) and
I’m trying to figure out what would be better.  Layering would be
better.

The secure gossip protocol
--------------------------

The secure gossip protocol in Prate provides a peer-to-peer reliable
authenticated publish-and-subscribe service to higher-level protocols.

A *journal* consists of a *public key* and an append-only sequence of
sequentially numbered *pages*, each of which is *sealed* with a
signature from the *private key* corresponding to the public key.
Each page consists of a *header*, a delimited sequence of bytes
interpreted as the page number and an arbitrary set of name-value
pairs; a *body*, which is an arbitrary sequence of any number of
bytes; and the seal, which is a cryptographic signature of the
concatenation of the header and the body.

When two *peers* are talking about a journal --- later we shall
discuss how this may come to pass --- which is identified by a *hash*
of the journal’s public key, they can ask one question:

- Please send me pages N and up from journal X, up to a maximum of M
  bytes.

The polite response to this question takes of one of the following
forms:

- I can’t or would prefer not to.
- I know pages numbered up to N’ from journal X, whose public key is
  Y.  Page N consists of 301 bytes: <....>.  Page N+1 consists of 1820
  bytes: <...>.  Page N+2 consists of 238332 bytes.

The positive response may include the contents of zero or more pages.
It includes the full public key, since the public keys use Ed25519 and
are therefore only 32 bytes, which is compact enough to always send.
The pages may be sent immediately or not until later; indeed, they may
not exist at the time they are requested.  Consequently N’ may be less
than the maximum page number sent.

The final information sent for a positive response is, at times, the
size of a page that was not sent because it was too large to fit
within the byte limit M.

Holders of private keys must ensure that they never seal two distinct
pages in the same journal with the same page number, and that they
never seal a page with a page number less than an already-sealed page
on the same journal; in this way journals must remain append-only.
They should start numbering the pages in each journal at 0 and number
them sequentially without skipping any numbers.  Peers must never send
pages that are not sealed, whose seal is invalid, or that have not
been requested, either by preceding N or by causing the total
responses to a request to exceed M.  They must never send a public key
that does not have the correct hash, either.

As in BitTorrent, it does not matter at all who the two peers are,
because any information published unencrypted in a journal is assumed
to be public, so sending it to any peer is okay; and, because the
pages in the journal are sealed, they authenticate themselves, so it
is equally valid regardless of who you got it from.

Peers can freely choose whatever retention policy they want for
journal pages, as well as choosing when and whether to make or fulfill
requests.  Of course if they do not have a copy of page N, they cannot
send it when requested, but in this case they may send page N+1, N+2,
etc., as long as they fit within the byte limit.  However, if they do
send any pages, they must send the lowest-numbered pages they have, or
(if they do not fit in M) their byte count.  They may not choose, for
example, to send page N+1 when they could have sent page N, or page
N+2 when they could have sent page N+1.

Again, as in BitTorrent, you might choose which peers and journals to
devote your resources to based on your past interactions with them
and/or their identities.  For example, if a peer asks you for pages
from journal X, you might ask them for pages from the same journal,
especially if you couldn’t satisfy their request.  And if they do
satisfy your requests, you might prioritize satisfying their requests
in the future, perhaps even subscribing to journals you aren’t really
interested in, but that they have expressed interest in.  As another
example, you might send requests for pages optimistically to peers who
you have no real reason to think can satisfy them.

One uncertainty: although sealed page numbers ensure that malicious
third parties cannot alter the history of an existing journal except
by unanimous replay attacks (a form of censorship), they do not ensure
that the legitimate author only publishes pages in order.  Including
the hash of the previous page in each new page, like Secure
Scuttlebutt, Bitcoin, and Git, would prevent the author from
publishing pages out of order.  Does this matter?

Journals, topics, and identities
--------------------------------

An *identity* is an agent in a distributed system, such as a human or
a running program.  A *topic* is a set of messages an identity might
want to subscribe to.  Prate’s gossip protocol, described above, does
not directly provide the ability to subscribe to or “follow” topics,
which is surprising because it is claimed to provide pub-sub.  It only
provides the ability to subscribe to journals, which is implemented by
asking other peers to send you pages from them.

Generally there is not a one-to-one relationship between journals and
topics, topics and identities, or identities and journals.  We can
implement both identities and topics to a significant extent as groups
of journals.  To subscribe to a topic, you somehow obtain a list of
journals that belong to it, then subscribe to all those journals.  To
publish to a topic, you create a new journal for your publications on
that topic, then somehow try to advertise your journal so that others
will subscribe to it.

Journals cannot usually be shared between identities, because, as
explained above, holders of private keys must ensure that they never
seal two distinct pages in the same journal with the same page number.
This means that, for example, if a person wants to publish both from
their laptop and from their cellphone, they will need to create one
journal on each, unless they are willing to take on the obligation of
assigning page numbers manually.  Otherwise, the possibility exists
that, while their cellphone is in airplane mode, they will seal a page
on their laptop, save it on their pendrive, accidentally run over the
laptop with their pickup, then seal another page on their cellphone
with the same page number and promulgate it, then later promulgate the
doppelganger page on their pendrive from a different laptop.  This
possibility is clearly intolerable, so they should either use two
separate journals or keep the private key on a centralized server that
both the laptop and cellphone use.

Signaling, advertising, discovery, spam, and denial of service
--------------------------------------------------------------

How does the new kid on the block make her first acquaintance?  Once
this is done, the acquaintance can introduce her to others as per the
Granovetter diagram, who can introduce her to still others,
progressively widening her circle of acquaintances.  But how can the
progress get started?

For example, suppose there’s a well-known journal (call it Factsheet
9) that periodically publishes new lists of journals that publish on
particular topics.  If you want to subscribe to news about wildfires,
you can subscribe to Factsheet 9, peruse its past pages for
announcements of wildfire journals, then subscribe to all those
wildfire journals.  Similarly for Google outages, time zone changes,
or new erotic fiction.  But if you want to *publish* news about a
wildfire, somehow you must persuade Factsheet 9 to list your new
wildfire journal.  How can you establish contact as a complete
unknown?

Whatever solution is adopted to this problem, allowing complete
unknowns to establish initial contacts, is vulnerable to Sybil attacks
and spam, and so it cannot be considered reliable.  But that does not
mean that no solution exists.

Alternative terminology
-----------------------

I’ve considered a number of alternative terms for “journal” and
“page”.  Perhaps “journal” should be “feed”, “stream”, “channel”,
“ledger”, “scroll”, “book”, “codex”, “file”, “hair”, “thread”, “tune”,
“battery”, “log”, “dynasty”, or “chain”, while perhaps “page” (the
unit of committing to a journal) should be “transaction”, “line”,
“drop”, “entry”, “scrap”, “chapter”, “cell”, “verse”, “slice”,
“parcel”, “morsel”, “packet”, “commit”, “king”, “block”, or “link”.
So we might say we append commits to a log, or lines to a file, drops
to a stream, or entries to a ledger, or kings to a dynasty, rather
than pages to a journal.

“Page” has the misleading connotations of mutability and a fixed
size. “Log”, “journal”, and “ledger”, and to a lesser extent “feed”,
have the right append-only connotation.

“Appending lines to a file” or “to a log” sounds reassuringly low-tech
and helpfully connotes variable-sized-ness, but misleadingly connotes
a size closer to 64 bytes than, say, 2048, which I think is more
likely in the sweet spot.  It also misleadingly connotes plain text,
and it might lead to confusion when we’re trying to talk about the
implementation: “What do you mean, the file is stored in several
files?”

[Secure Scuttlebutt](secure-scuttlebutt.md) uses “feed” and “message”,
and following that convention might help comprehensibility for people
who know SSB.  [Kafka](ccn-streams.md) uses “topic partition”
(described as an “ordered ‘commit log[]’”, leading me to favor “log”
and “commit”) and “event”.

Detached and batched signatures
-------------------------------

    22:43 < xentrac> I hadn't thought about separating the signatures from the commit bodies.  that could 
                     certainly speed up verification of updates
    22:44 < xentrac> like, if I have commits 1-8, and you send me commits 9-16, I can just verify the commit-16 
                     signature instead of the signatures of all eight commits
    22:45 < xentrac> OTOH I'd still have to get the other seven signatures in order to be able to send commits 
                     9-10 to somebody else who doesn't have time/bandwidth for the other six yet
    22:46 < xentrac> because I'd need to send them commit 10's signature.  and then what if it turns out to be 
                     invalid?
    Day changed to 07 Oct 2020
    19:37 < Remosi> xentrac, instead of the signatures for all of them, you can just transmit the hashes
    19:37 < Remosi> and then the final signature
    19:37 < Remosi> the hash presumably being no more bits than the signature.
    19:46 < xentrac> you're saying that I can send mr. low bandwidth the full commits 9 and 10, and then only the 
                     hashes of commits 11-16, and then the signature for commit 16?
    19:48 < xentrac> I guess that's possible if the commit hashes in the chain are calculated over the previous 
                     commit hash and a hash of the current commit body
    19:53 < Remosi> it does mean however you have to send hashes for 11-16 rather than just a signature for 10.
    19:56 < xentrac> yeah, but maybe that's a reasonable tradeoff for not having to receive signatures for 11-16
