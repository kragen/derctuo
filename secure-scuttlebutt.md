Some notes from reading the [Secure
Scuttlebutt](https://ssbc.github.io/scuttlebutt-protocol-guide/)
protocol specification.  I'm coming into this with some prejudices
since what I've heard is that SSB is a pretty good design, similar to
some things I've been toying with for some time, but with some fatal
flaws that make interoperability very difficult (which turns out to be
sort of true, due to problems with JSON canonicalization).  So I'm
looking at it with more of an eye to implementing a similar but
incompatible protocol than with an eye to implementing SSB itself.

My overall sense is that the protocol seems pretty sloppy, although
probably workable for its intended purpose.

The formatting of the document seems okay but I'm not wild about
essential parts of it being represented as PNGs.

Ed25519 keys as identities is reasonable; might be better to use
hashes of them.  See below.

Broadcasting discovery packets once a second ("advertising") seems
excessive, though maybe that's because I've been thinking a lot about
low-bandwidth networks like FidoNet, LoRa, and shortwave radio.  Maybe
it would be better to broadcast on the order of once every 256
seconds, after detecting a network change, or after seeing an
advertisement for a previously unknown peer.  The advertisement packet
format seems to be about 50 bytes, so the advertisements alone suck up
400 bits per second per peer; ten such peers would render a
4-kilobit-per-second channel useless.  It would take fewer peers to
saturate the same connection if framing and lower-level headers also
consumed bandwidth.

I don't think the terminological distinction between "identity"
(sometimes called "feed ID") and "feed" pulls its weight.

I like the terminology of "feed" for "all the messages posted by that
identity", and I like the one-dimensional URL-like serialization of
@keys, %messages, and &blobs.  I'm not sure it's worth it to make them
*not* URLs — particularly since keys, the protocol element it's most
important to keep lightweight (for example because of their use in
invite codes), include a verbose namespace identifier ".ed25519" at
the end!  (However, I'm not a fan of the alternative JSON
representation of the discovery packet used in the pub message format,
where, instead of being concatenated into a single string, the host,
port, and feed ID/identity are stored in the three properties "host",
"port", and — can you guess? "feed"? "id"? "identity"? — no,
"key", of a JSON "object".)

To elaborate, an Ed25519 key is 32 bytes.  Encoded in base64 it's 44
bytes, for 12 bytes of encoding overhead, and the ".ed25519" at the
end adds another 8 bytes.  Ed25519 is not
quantum-cryptography-resistant and birthday attacks are not useful
here, so if we are willing to accept (classical) brute-force
resistance of only 2¹¹⁹, we need only use 120 bits of public key hash
as an identifier, or 15 bytes; this base64-encodes to 20 bytes.  If,
at some future time, non-Ed25519 keys are desired, they can be
signified by beginning their representation with one of the 31
printable ASCII characters that are not valid Base64 — namely, space
or any of the punctuation except for '=', '+', or '/'.  Thus, instead
of the representation 'FCX/tsDLpubCPKKfIrw4gc+SQkHcaD17s7GI6i/ziWY='
specified in SSB, we can b64decode that, sha256 it twice, take the
first 15 bytes, and b64encode the result:

    >>> k0 = base64.b64decode('FCX/tsDLpubCPKKfIrw4gc+SQkHcaD17s7GI6i/ziWY=')
    >>> sha256 = lambda s: hashlib.sha256(s).digest()
    >>> base64.b64encode(sha256(sha256(k0))[:15])
    'c71K2cieLixgzReT8TkT'

I argue that '@c71K2cieLixgzReT8TkT' is a more reasonable feed ID than
'@FCX/tsDLpubCPKKfIrw4gc+SQkHcaD17s7GI6i/ziWY=.ed25519', and a
classical (non-quantum) brute-force attack on it still requires an
expected 2¹¹⁹ hashing operations, about 21 quintillion machine-years
at one hashing operation per machine-nanosecond.  The drawback is that
in order to actually validate any signatures you need to somehow
obtain the whole 255-bit public key, so the protocol needs to have
some way for you to get it.  I think this is probably a good tradeoff.

You could imagine the full URL for a peer owning that key might be
something like `p9://one.butt.nz:8008/@c71K2cieLixgzReT8TkT` or
`p9://138.68.8.185:8008/@c71K2cieLixgzReT8TkT`, which is noticeably
shorter than *just the key* in SSB's format, let alone the whole
discovery packet, which I am mostly retyping here because in the
original document it's in PNG:

    net:192.168.1.123:8008~shs:FCX/tsDLpubCPKKfIrw4gc+SQkHcaD17s7GI6i/ziWY=

So for example the URL can be encoded as a version 3 QR code (29×29),
while the SSB discovery packet requires version 4 (33×33).  The
"invite code" explained later on is a total fail; the example is
`one.butt.nz:8008:@VJM7w1W19ZsKmG2KnfaoKIM66BRoreEkzaVm/J//wl8=.ed25519~r4hIBk7KC7a9Gknj7Qiuuo4+Et/TS2rjgl6gYgw3OIM=`,
which I also retyped because it's also in a PNG.  This requires a
version 6 QR code (41×41).  See later for invite codes.

If there's some kind of advertisement service where you can publish
your current IP:port, then it might be adequate to say
`p9:@c71K2cieLixgzReT8TkT`, which is a version 2 QR code (25×25), 43%
smaller than the SSB discovery packet above.

What's with this `p9` idea for an URL scheme?  Well, shorter is
better, as long as it doesn't pose too much risk of a collision.
There are only nine two-character URL schemes [in Wikipedia's
list](https://en.wikipedia.org/wiki/List_of_URI_schemes), one of which
is the `ni:` scheme for naming data by hash; [the W3C also lists `bk:`
and `kn:`](https://www.w3.org/wiki/UriSchemes), one of which was
partly my fault.  [IANA lists provisional
registrations](https://www.iana.org/assignments/uri-schemes/uri-schemes-1.csv)
for `qb:` — and `ssb:`!  Also, only ten of IANA's list of 335 schemes
(provisional and otherwise) use digits.  So the risk of an URL scheme
clash is quite low.  "p" is for "prate", and 9 is an auspicious number
signifying permanence in Chinese.  (You might want to use the
"URL-safe" variant of base64 in which `-` and `_` are used instead of
`/` and `+`; in that way you could still interpret `/` as a path
separator in the usual way.)

A thing I'm not entirely sure about is whether you'd want to use the
same *identity* to talk about two different *topics*.  Evidently, for
example, you use a "long term public key" not only to identify a feed
(and presumably sign messages or blocks of messages on the feed,
though I haven't gotten that far yet) but also to authenticate
incoming connections as a server and to authenticate yourself on
outgoing connections as a client.  I think it might be worthwhile to
separate these functions.  (Also, does the key-exchange protocol
support "name-based virtual hosting"?)

I'm not entirely sanguine that Dominic Tarr has evidently [invented
his own key exchange
protocol](https://dominictarr.github.io/secret-handshake-paper/shs.pdf),
but it does purport to provide security properties that aren't present
in the other key exchange protocols I know about.  However, I am not
hip to the state of the art in key exchange protocols, and never have
been, actually.  I wonder how well it protects against DoS attacks.
The fact that it's built on NaCl is promising but of course not a
guarantee against misuse.

The box stream protocol headers are rather bulky at 34 bytes.  At 300
baud N81 that's over a second for just the header.  For an 8-byte
payload that works out to 425% overhead.  However, it may not be
feasible to offer the cryptographically strong authentication
purportedly offered by this protocol at a significantly lower cost
than that.

By contrast to the box stream protocol, the rather bulky
feed-ID/public-key/identity serialization, and the JSON RPC protocol
*body*, the RPC protocol *header* is bummed to within an inch of its
life, with five bit fields in one byte — which dealigns the following
fixed-size binary numerical fields.

The SSB protocol doc isn't always clear about the usual must/should
distinction; at one point, for example, it says, "JSON messages don't
have indentation or whitespace when sent over the wire."  Does that
mean we should reject JSON messages containing whitespace?  What if
it's inside a string?  In another case, it says, "Because this is the
first RPC request, the request number is 1."  What should you do if
the first RPC request you get on a stream is numbered 0, as any sane
person would do, instead of 1?

I'm not sure "createHistoryStream" is a good name for "subscribe".  I
mean it sounds more like "publish" than "subscribe".

The first example RPC response seems like it might have a typo:

     "key": "%XphMUkWQtomKjXQvFGfsGYpt69sgEY7Y4Vou9cEuJho=.sha256",

I thought previously we said "%" was for messages, not keys?  Also,
this seems to be a hash, not a key.  (And as such it could use the
`ni:` URL scheme mentioned earlier, or for that matter `magnet:`.
Would those be better?)

And we start to see the difficulty with the message signature scheme:
we have a JSON structure under the name "value", then what is
purported to be an Ed25519 signature.  But Ed25519 cannot sign JSON;
it signs blobs.  Perhaps later we will see how this is resolved.

The pairing of request 1 with response -1, request 2 with response -2,
and so on, is a bit goofy.  And the name "RPC" doesn't seem entirely
apt.  But these are minor details.

A perhaps more serious issue — for some applications, anyway — is
that, when you request the messages from a feed, you apparently have
no idea if you are going to receive 150 bytes or 150 megabytes of
response, and no way to stem the flood if it overwhelms you, other
than the usual TCP mechanisms, assuming you're speaking over TCP.  (A
mechanism is given "to abort a stream before it is finished" but it's
of the XON/XOFF flavor, not the ENQ/ACK or TCP/ZMODEM sliding-window
type, so a FIFO in the system will totally defeat it.  Later on we do
see that `createHistoryStream` has a `limit` option, but it's a limit
on the number of messages, not a number of bytes.)

I think the partitioning between messages and blobs is probably a good
idea for many purposes, though the example messages given for the
first few requests don't refer to any blobs; they just have text
bodies.  So the introduction of *blobs.has* (or, as it's spelled
elsewhere, `["blobs","has"]`) is a bit startling.

I worry a little bit about the potential information leaks associated
with *blobs.has*.  Should I consider the set of blobs that my
Scuttlebutt client *has* to be public information, or at any rate
visible to everyone I connect to?  Might having a blob indirectly
reveal something I consider private?

As I've mentioned previously in personal communications, I don't think
there's any benefit in a feed like the Scuttlebutt feed to including
the hash of the previous message in each message.  Including such
hashes in general cannot defend against message *blocking*: if nobody
is willing to give you Alice's message #4, then the fact that you know
its hash from reading Alice's message #5 does not in itself help you
to find out message #4's content.  Rather, including such hashes is
designed to prevent attackers from *silently* blocking messages
(without blocking all following messages) and from *altering*
messages.  But Alice's signature on message #4, if calculated over
something that includes the sequence number 4, already defends against
those two attacks.

Specifying within the message content the hash algorithm to be used to
sign the message also seems like a bad idea to me, although a
relatively harmless one — as with the link to the previous message, it
only wastes a little bandwidth.

The `["Message format", "signature"]` section finally explains the
JSON canonical serialization used for signatures, which is kind of
terrible; it refers to the ECMA-262 6th-edition spec for
`JSON.stringify`!  And it's a different JSON serialization from the
one used on the wire, because it contains mandatory whitespace.
Moreover, it entirely fails to mention the order of dictionary keys,
and the example message in the SSB document to which it refers does
not have the keys sorted in any discernible order.  ECMA-262 [does not
appear to specify the dictionary key order
either](https://www.ecma-international.org/ecma-262/6.0/#sec-json.stringify):
stringify calls SerializeJSONProperty which calls SerializeJSONObject
which calls EnumerableOwnNames which uses the ordering of the
[[Enumerate]] internal method which, in the 6th edition, explicitly
says ["order of enumerating the properties is not
specified"](https://www.ecma-international.org/ecma-262/6.0/#sec-ordinary-object-internal-methods-and-internal-slots-enumerate),
although, as I understand it, the committee is going to standardize
that iteration happens in insertion order, or has already done so.

Later, contradicting what the "Signature" section says, the "Message
ID" section says, "Like with signatures, dictionary keys must appear
in the same order that you received them."  This is at least an
implementable specification, although it precludes the use of most
JSON libraries.

Basically this is the same design error as XML canonicalization and
ASN.1 DER, only botched.  If you google "How Not To Sign JSON" this is
literally what you will find.

On the `createHistoryStream` semantics, I think six options is too
many.  The basic request ought to be "please send me messages on feed
X, starting from sequence number Y, up to a limit of Z bytes," and the
response ought to indicate when all the known messages have been sent
and the peer is now waiting for new messages to forward on to you.
There's no need to have the option not to know when you're up to date
(it costs one bit on each message at worst), no need to have other
ways to fetch messages, no need for an option to omit messages that
arrived at the peer before you sent your subscribe, and no need for
the peer to tell you when it received messages, much less an option to
turn that behavior off.

I do think the decision to handle feeds and blobs separately is good.
In fact, I think that in many cases they should be even more separate
than they are in SSB.  The lack of this separation is one of the
weaknesses in Van Jacobson's CCN.

Blob IDs do not need to be dozens of bytes long (again, presuming no
quantum cryptanalysis, 120 bits should be fine), and there's no need
to have separate get and getSlice procedures ("methods"!?), nor
multiple argument formats.  A single request to fetch up to X bytes
from blob Y starting from byte offset Z, whose response includes the
actual blob size, is sufficient; if you want the whole blob then you
can just set X to be very large, or, after the first request, use the
actual remaining size of the blob.

A better hashing scheme such as BLAKE3 would enable the verification
of partial blobs (down to 1 KiB) as well as entire blobs, with a
little bit of extra metadata.  (BLAKE3 is also about 30 times as fast
as SHA-256 on modern multicore SIMD hardware.)  I'm not sure exactly
how truncating BLAKE3 hashes affects its security.

I need to come back and read the wants/haves stuff more later, except
that I really don't like the name "createWants".  It seems like a
reimplementation of CCN, inheriting some of its weaknesses.

In a lot of cases I would like to store my blobs in a totally
different system from the gossip system used for pub/sub broadcasting.
For example, I might want to use a DHT to assign blobs to blob
servers, rather than replicating them to every peer.

I think "feeds can follow other feeds" is... not a useful idea.  But
it's entirely separable from the rest of the protocol.

"Invite codes" serve a couple of purposes, but I think they can be
served more easily.  I'll have to read the invite-code stuff later.

Posting private messages on your regular feed, but encrypted, is
probably a reasonable thing to do, but in other cases it would be more
useful to create a feed for a particular person-to-person conduit.
However, encrypting messages and then posting the ciphertext as base64
strings in JSON is a stupid thing to do.  It's like pre-yEnc
alt.binaries Usenet.
