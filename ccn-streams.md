Suppose we take a Kafka approach with Merkle trees to publishing
activity streams.  If we presuppose an existing decentralized reliable
retrieve-by-hash service that returns stored files when presented with
a hash of their contents, we nearly have a workable decentralized
publishing system.  All that’s needed is an unreliable
publish-subscribe system to provide updates, and coupled with an
aggregation system, it can work even at very low bandwidth.

The retrieve-by-hash service
----------------------------

The retrieve-by-hash service provides a single function, `get`; given
some hash H, `get(H)` returns a blob (a file, a byte string) of some
arbitrary size that hashes to `H` using some secure hash function.  To
be concrete, let’s say it uses SHA-256 with the high bit set to 0, so
the hash is fixed at 32 bytes.†

The interpretation of application blobs is outside the scope of this
note, except to note that they can contain the hashes of other blobs
and may also contain things of interest such as text, computer
software, historical stock prices, or pleas for help; and they can be
encrypted.  In file `ccn-static-hypertext.md` are some thoughts on
building a hypermedia and software archival layer on top of this
simple service.  The implementation of the service is also outside the
scope of this note.

However, since the hash is computed over the contents of the blob, it
is (conjectured) infeasible to compute the hash for a blob whose
contents are to be chosen in the future.  So no blob can contain the
hash of a blob that was created later; hash references can refer only
to past information, not future information.  So this service does not
provide any way to find out whether something has happened, such as
whether the Bitcoin price has exceeded US$10000 again yet, or to
send or receive messages, or to update any information.

But a small notification message delivered over a publish-subscribe
channel — the size of a single hash, or even a bit less — is
sufficient to link to an arbitrarily large quantity of data stored in
the service up to the time when the message was sent.  The
notification need not itself contain a signature, since it can link to
a signature stored in the blob store, but it may be convenient to
include a signature so that subscribers need not sort through spam or
malicious notifications.

*****

† The high bit is set to 0 to preserve the option of upgrading to
other algorithms in a possible future where SHA-256 is broken; new
kinds of hashes can be added in a backward-compatible fashion by
setting their high bits to 1, and a successful attack on SHA-256 then
cannot replicate those hashes.  Security against Kardashev Type 3
adversaries probably requires a longer hash, but 255 bits should be
enough for a Kardashev Type 2 adversary with quantum computers.

The Kafka architecture
----------------------

[Kafka][1] pretends to be a publish-subscribe system, but it’s really
an append-only fileserver.  In a publish-subscribe system, subscribers
(“clients” or more specifically “consumers” in Kafka lingo) subscribe
to channels (“topics”) and are notified immediately of new events
published (“produced”) on those channels.  The way this works in Kafka
is that a consumer makes a TCP connection to the server (“broker”)
that is the “leader” for a channel† and “fetches” new events on that
channel, which is described as an “ordered ‘commit log[]’”.  Each
message added to this log is assigned a sequence number called an
“offset”; the message’s payload is an opaque byte array with a small
amount of header metadata.

There are some 46 request types in [the Kafka protocol][0], but we are
only concerned with the requests Fetch and ListOffset.
`Fetch(replica_id=-1, max_wait_time, min_bytes, topic_name, partition,
fetch_offset, max_bytes)` returns all the messages on `(topic_name,
partition)` starting from `fetch_offset`, waiting up to
`max_wait_time` to finish responding if `min_bytes` bytes are not
initially available, with a response size limit of `max_bytes`.
`ListOffset(replica_id=-1, topic_name, partition, time=-1)` fetches
the “log end offset” that will be assigned to the next message posted
to the channel `(topic_name, partition)`, or optionally returns the
offset of the oldest retained message `(time=-2)` or the oldest
message before a given timestamp (given as the value of `time`.)

So, when you first connect to a Kafka broker, you can ask it what
messages are retained on a channel with ListOffset, and then you can
fetch some or all of those messages with Fetch, and you can send a
Fetch request to get any future messages.  It’s up to you to remember
what offset you have gotten messages up to; the broker doesn’t know
and doesn’t care.  If you lose a connection and reconnect, you can
send another Fetch with the offset of the next message you haven’t
gotten yet, and it may return immediately, or it may block for up to
`max_wait_time` — and its unblocking is the form of asynchronous
notification provided by Kafka.

A benefit of not storing your session state on the server is that
server failures can’t lose it, and server security problems can’t
corrupt it; when the new server comes up, you can just continue
reading messages where you left off.  As long as the assignment of
offsets to messages remains consistent, there is no risk that a
message will be duplicated.

By waiting for consumers to fetch messages in this way, Kafka has less
overload conditions — consumers that begin falling behind do not
consume excessive network buffers or other memory on the server, nor
do their TCP connections time out.

Kafka unfortunately does have problems in which messages can be
duplicated because of flaky connections between brokers and
*publishers* (“producers”).  The problem is that the offset is
assigned by the broker, not the publisher, because multiple publishers
can publish to the same channel.  Jay Kreps felt that this was a
reasonable tradeoff given that the alternative would be potentially
the assignment of conflicting offsets every time any of thousands of
disks across LinkedIn’s server farm failed.  But recent versions of
Kafka have added an additional set of producer IDs and sequence
numbers to support message deduplication in this case.

A big issue in some environments would be that, since the Kafka broker
doesn’t know what consumers might exist, it can’t safely delete
messages once they’ve been delivered.  Kafka mostly tries to solve
this problem by encouraging you to store your messages on cheap
multi-terabyte spinning rust, and reducing the cost of that as much as
possible, so you can delete your messages a week or two out instead of
after a few hundred milliseconds.

Due largely to this design, Kafka has been historically the fastest
message queue system out there.  RabbitMQ can handle 100,000 messages
per second on a normal PC, ØMQ can handle about 2.5 million (without
persisting them to disk), and [Kafka is just as fast while persisting
to disk and replicating][2], and can scale up from there, [for example
to 7 million messages per second across a cluster at Criteo][3].
Apache Pulsar is a new alternative designed to be faster.

And, fundamentally, all Kafka is doing is allowing producers to append
batches of messages to a logfile, and allowing all the consumers to
read that logfile and get notified when it gets extended.  Is there a
way we can provide that service with a decentralized system?

*****

[0]: https://cwiki.apache.org/confluence/display/KAFKA/A+Guide+To+The+Kafka+Protocol
[1]: https://kafka.apache.org/documentation.html#design
[2]: https://engineering.linkedin.com/kafka/benchmarking-apache-kafka-2-million-writes-second-three-cheap-machines
[3]: https://www.slideshare.net/RicardoPaiva17/how-is-kafka-so-fast

† The broker is actually the leader for, in Kafka terminology, a
“partition” of a “topic”, in order to support load balancing of a
single topic both among brokers and among subscribers, but this is a
useless epicycle; since it’s transparent neither to publishers nor
subscribers, it’s equivalent to just using multiple topics.  As for
leaders, partitions have leaders because Kafka is a clustered system
that automatically replicates data across a cluster of Kafka servers,
but it is essential to avoid the assignment of the same offset in the
same channel to two different messages.

Reading a logfile with a Merkle tree
------------------------------------

A Merkle tree node is either an “internal” blob containing just the
word “tree” followed by zero or more hashes, or a “leaf” blob
containing just the word “leaf” followed by some data.  The value of
the second one is the data after the word “leaf”; the value of the
first one is the concatenation of the values of the blobs to which the
hashes refer.  Given a long string, we can compute a Merkle tree for
that string made of blobs of about 4 kibibytes by first dividing it
into 4-kibibyte chunks, prepending “leaf” to each of them, then
creating a first level of internal blobs representing concatenations
of up to 128 of these leaf blobs (up to 512 kibibytes per first-tier
internal blob), and if there’s more than one of those, then creating a
second tier of internal blobs of up to 128 of the first-tier internal
blobs (up to 64 mebibytes per second-tier internal blob), and so on
until you have a tier that consists of just one root node.

Suppose you have the hash of a Merkle tree node that you somehow know
that is the root node of some version of Barbara’s event log.  So you
get the corresponding blob from the retrieve-by-hash service and look
at it.  If it’s a leaf node, you already have the whole event log, but
if it’s an internal node, you have to get the blobs it refer to from
the retrieve-by-hash service if you want to read the log contents.

Suppose Barbara wants to publish another event.  She appends the event
to the end of her event log, perhaps just appended onto the last
leafblob or perhaps broken into many leafblobs, and then makes a new
version of its parent internal node, and if any, *its* parent, and so
on.  Then somehow she publishes the hash of this new node.

Now, suppose you get another hash that you somehow know represents the
root of this new version of Barbara’s event log.  You get it; both
this one and the other one are internal blobs.  You can look at the
hashes to see which ones are new, and fetch just those.  As long as
Barbara has published less than 64 mebibytes so far, you only need to
fetch two levels of internal blobs (an overhead of 8 KiB, plus 1/128
of the weight of Barbara’s new data), plus Barbara’s new data.

We can augment the Merkle tree internal blobs with size information
for each subtree so that it becomes easy to navigate to a particular
offset.  We can augment the root blob with a cryptographic signature
so that, if you somehow get hold of the root blob hash, you can verify
that Barbara did indeed publish that version of her event log, with no
further information.  We can make a long string of such signed root
blob hashes for different people, each labeled with that person’s
public key hash, and make a Merkle tree of *that*, and publish *its*
root blob hash.  But how do we get that root blob hash out to the
masses?  The retrieve-by-hash service can’t do it.

The paging channel
------------------

Traditional phone networks work by setting up a “call”, a reserved
fixed-bandwidth channel between a pair of conversants, who can then
exchange data over it.  Different phone systems have different kinds
of channels to allocate to a call: a copper pair, a frequency on an
FDM coax cable, a SONET timeslot, an ISDN channel, or an AMPS FM
channel pair, for example.  Once the call is set up, it is free from
interference; except in the case of equipment failure, it offers
guaranteed bandwidth and reliability, and there is no need to resend
data due to collisions with other senders as there is with Ethernet.

However, before the call is set up, some sort of communication channel
needs to exist to bootstrap it.  This is the so-called “control” or
“signaling” or “paging” channel, and typically it provides a
best-effort kind of service, with no guarantees of bandwidth or
reliability, because the channel is shared among many uncoordinated
users.  (USB demonstrates that this is not the only possibility.)

Even a low-bandwidth paging channel can distribute the hashes of new
root blob hashes pretty easily; you only need to transmit 256 bits,
and if you only need to be secure against current attacks, you only
need to transmit about 80 bits.  There are several ways to implement
such a channel: burning Bitcoin, shortwave radio, classified ads in
the New York Times, gossip among locally connected nodes, IRC
channels, comment threads on long-ignored news articles, timing
channels in DNS TTL countdowns from shared caching DNS servers,
shining lights on tall buildings at night, and so on.

If you are somehow in a position to broadcast such a hash, how do you
choose which one to broadcast?  Maybe you’d broadcast the hash of the
index that was most up-to-date and had the largest number of
publishers, perhaps creating your own by piecing together other
indices you had access to.  Or maybe you’d create your own by removing
all the publishers you suspect of anti-Islamic views or publishing
misinformation about the covid pandemic.  Maybe you’d include
thousands of “publishers” selling penis pills, or maybe you’d copy
someone else’s index and remove the penis-pill sellers.  It all
depends on your desires.

But what about the people who retrieve that hash?  What do *they* want
to do?  What kinds of paging channels will be responsive to their
needs instead of the needs of whoever has the most money or the
brightest arc lights?