In today's internet, IP addresses are often assigned dynamically and
unpredictably, but not changeably at will.  So if you want to send a
person IP packets, you need some way to find out what their current IP
address is.  If they're behind a NAT, you may also need to find out
what their current port is on that IP address so that you can do NAT
hole-punching.  (Some kinds of NAT don't even support that, but most
do.)

The IPv4 + UDP port data is 48 bits.  If you could get that data, or
most of it, to your contact, the two of you could establish a UDP
connection.  So you need some kind of rendezvous point.

Here's a permissionless, harmless, efficient solution vaguely similar
to private information retrieval protocols.

Background on the DNS
---------------------

Let's consider the side channel associated with a caching DNS server
and a domain name with a relatively long TTL value, such as 18000
seconds.  For example:

    100.172.217.172.in-addr.arpa. 17429 IN	PTR	eze06s02-in-f4.1e100.net.
    eze06s02-in-f4.1e100.net. 34228	IN	A	172.217.172.100

From an authoritative server the TTL on eze06s02-in-f4 is actually
86400 seconds, so what we're seeing here is that someone sharing the
DNS server with us did a lookup of this domain name 86400-34228 =
52172 seconds ago, plus or minus a second or so.  They have
effectively written about 16.4 bits into this DNS server's cache,
which now anyone who the DNS server is willing to respond to can read.

There's a "norecurse" bit you can set on a DNS request.  This doesn't
prevent the DNS server from returning you a value from its cache, but
it does prevent it from going and fetching the value.  This is useful
because it permits a nondestructive read of this timing data.  Here we
see two identical queries on the public DNS server 8.8.8.8, one of
which fails with SERVFAIL, and the other of which succeeds, because
8.8.8.8 is not only anycasted, but also its local instance seems to be
load-balanced on a per-request basis:

    $ dig +norecurse @8.8.8.8 eze06s02-in-f4.1e100.net.

    ; <<>> DiG 9.10.3-P4-Ubuntu <<>> +norecurse @8.8.8.8 eze06s02-in-f4.1e100.net.
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: SERVFAIL, id: 2151
    ;; flags: qr ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;eze06s02-in-f4.1e100.net.	IN	A

    ;; Query time: 44 msec
    ;; SERVER: 8.8.8.8#53(8.8.8.8)
    ;; WHEN: Wed Oct 07 13:36:44 -03 2020
    ;; MSG SIZE  rcvd: 53

    $ dig +norecurse @8.8.8.8 eze06s02-in-f4.1e100.net.

    ; <<>> DiG 9.10.3-P4-Ubuntu <<>> +norecurse @8.8.8.8 eze06s02-in-f4.1e100.net.
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12898
    ;; flags: qr ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;eze06s02-in-f4.1e100.net.	IN	A

    ;; ANSWER SECTION:
    eze06s02-in-f4.1e100.net. 21586	IN	A	172.217.172.100

    ;; Query time: 42 msec
    ;; SERVER: 8.8.8.8#53(8.8.8.8)
    ;; WHEN: Wed Oct 07 13:36:47 -03 2020
    ;; MSG SIZE  rcvd: 69

The DNS cache rendezvous protocol
---------------------------------

Suppose you have a prearranged list of 128 DNS servers somewhere on
the internet that do not use load balancing, are not anycast, and
probably are willing to provide recursive, caching DNS resolution to
each of you.  And you also have a prearranged list of 128 long-TTL
DNS records.

What we are going to do is store a 50-bit value in this 128x128 matrix
of caching DNS servers by splitting it into ten 5-bit chunks, and
storing each 5-bit chunk in the cached TTLs of these 128 shared
recursive caching DNS servers.  First, we take the key we want to
associate information with and we hash it to produce a long random
bitstring.  Each 14-bit chunk of this hash identifies one bucket in
the 128x128 matrix: a particular DNS record on a particular caching
server.  We are going to store each 5-bit chunk of information
redundantly in 5 such buckets as the low-order 6 bits of the clock
when we make the request, except for the least significant bit, which
is not reliable.  In order to do this, we send a single 50-byte packet
to the indicated server in the middle of the indicated time interval,
requesting a recursive retrieval of the indicated DNS record.

If our request is successful, and the record was not previously in the
cache, the server will cache the record for its TTL.  So we have
successfully published 5 bits of data in such a way that the DNS
server will send them to anyone who subsequently requests the same
record before the TTL expires.  Then they need only add the remaining
TTL to their current clock and subtract the origin TTL to find out
when the original request was sent to a precision of one second.

But the request may not have been successful, or the record may have
been previously in the cache.  So we store the same data in 4 more
buckets, which in general will be on other DNS servers.  Now, someone
who wants to read this 5-bit chunk of data can make the same 5
requests --- though ideally with the no-recurse bit turned on, so that
they won't prevent the data from being published in the future if it's
currently not published --- and simply take the most common value from
among the results.  Really they probably only need to read two or
three of the buckets on most occasions, since two or three equal
values is already very strong evidence.

Repeating this process 9 more times stores or retrieves 50 bits of
data.  Of these, 48 are the IPv4 address and port at which to contact
the publisher of the advertisement.

So advertising in this way requires sending out about 50 packets of
about 50 bytes each, about once every 5 hours, and receiving the same
number of response packets of about 70 bytes each; this works out to
about 1-2 bits per second both inbound and outbound.  Retrieving such
an advertisement requires only about 25 requests and responses.

The choice of using the low 6 bits of timestamp imposes a minimum
latency of 64 seconds on publishing new data (which could be reduced,
except in cases of strong interference, by XORing the data stored in
each bucket with more bits derived from the key); if a longer latency
were acceptable, you could store more bits per bucket, thus requiring
fewer buckets, fewer packets, less bandwidth, but more latency.  For
example, with half an hour of latency, you could get 10 bits per
bucket, not counting the ignored LSB, rather than 5.  Inversely, you
could get latency down to 4 seconds by storing only one bit per
bucket.

In a sense, although in absolute terms the cost is very low, in
relative terms it's fairly high: to publish 48 bits of data --- 6
bytes --- you send out 2500 bytes and receive 3500 bytes.  That's
three orders of magnitude of bloat.  It's only efficient in absolute
terms because the service required is so minimal.

A large number of publishers can use the same 128x128 matrix as long
as they aren't trying to stomp on each other's keys, because each one
only uses 50 out of the 16384 buckets.  However, it's easy for anyone
who has the whole matrix to deny service to everyone.

A possible partial defense against that is to distribute different
versions of the matrix from a central authority to different
participants in the system, having for example two possible
alternatives for each row and two possible alternatives for each
column, half of which are concealed from each participant.
Geographically distant participants will share, on average, half the
headings and one quarter of the buckets, and so mildly more queries
will be needed.

Another partial defense would be to use a much larger matrix, like a
million by a million, which preliminary tests suggest is feasible (see
below).  Then anyone who wants to flood the whole matrix needs to send
out *one trillion packets*, like, fifty terabytes.  Every five hours:
ten terabytes an hour.

Any attacker who knows the key of a publisher is likely to be able to
jam their broadcasts.  This suggests that perhaps keys should be
per-relationship, not per-identity: if Alice uses one key to announce
her location to Bob and another to announce it to Carol, then Bob
can't jam the information Carol is reading, unless Carol tells him the
key or he can jam essentially the whole matrix.

A publisher might need to use two or three keys for rapid failover
when its IP address changes unanticipatedly, since it can't overwrite
its previously published data.

Of course there are more efficient error-correction codes than
repeating each symbol N times, and using these might be worthwhile.

There are 300 million servers currently providing this service
--------------------------------------------------------------

One key question I didn't know about when I started this is how many
publicly-accessible recursive DNS servers are still out there.  I
started out fairly confident that the answer is "more than 128".  I
think the answer is actually "hundreds of millions" because the very
first random IP address I generated, 39.188.24.230, happened to be
willing to answer my DNS query:

    $ dig @39.188.24.230 www.google.com.

    ; <<>> DiG 9.10.3-P4-Ubuntu <<>> @39.188.24.230 www.google.com.
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37948
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

    ;; QUESTION SECTION:
    ;www.google.com.			IN	A

    ;; ANSWER SECTION:
    www.google.com.		141	IN	A	31.13.95.38

    ;; Query time: 423 msec
    ;; SERVER: 39.188.24.230#53(39.188.24.230)
    ;; WHEN: Wed Oct 07 14:16:44 -03 2020
    ;; MSG SIZE  rcvd: 48

The next hundred or so I generated got me 5 answers (all different!),
so probably about 5% of all the possible IPv4 addresses have working
(?) recursive DNS servers that are willing to answer queries from my
Argentine residential address.  That's about 200 million currently
existing and accessible servers.  Presumably a tiny fraction of them
are anycast or dynamically load-balanced like 8.8.8.8, while the rest
would work fine.

Further random sampling refines that estimate to about 7%, which is
about 300 million servers.  I generated 1000 random IPv4 addresses,
sent a DNS query for www.google.com to each one (once), and got back
67 responses and 934 timeouts.  Not sure where the 1001th request
went.

Detectability
-------------

You could easily tell someone using this technique from a normal DNS
user: if publishing, they're sending DNS queries with recursion turned
on to several different DNS servers.  Stub resolvers send queries with
recursion turned on, but normally only to your ISP's nameserver, or to
8.8.8.8 or opendns or alternic or something, not to 50 different
servers.  Someone only reading would look like someone running their
own caching DNS server, in that they're sending out queries with
recursion turned off, except that many of their queries are getting
non-authoritative results.  Queries with the NR bit set generally will
get either authoritative results or no results; it's very unusual for
someone to send a no-recursion query and just happen to get a
successful response from some random cache.

The publisher might look like a sysadmin trying to debug a DNS
problem.  I can't think of any activity that would look like the
person trying to read.

That said, I don't know what kind of offbeat DNS things happen in the
wild nowadays.  Maybe there's some common DNS implementation bug that
looks just like this.

Alternatives
------------

Other permissionless signaling channels for such low-bandwidth
rendezvous tricks include blog comment sections, wiki edits, Freenode,
OFTC, Tor hidden services, altering the latency of publicly-accessible
servers on low-bandwidth connections by packet-flooding them,
shortwave radio, moonbounce, Usenet, web forums, and IM service
statuses.  Most of these impose significant social costs on others,
could disappear at any time, or have other drawbacks that this public
DNS side channel does not have.
