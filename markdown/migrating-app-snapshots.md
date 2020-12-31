Consider the problem of migrating a running program on demand to
whatever computer you have handy.  Perhaps a “master” copy of the
program’s running image lives on a “home server”, and when you want to
use a device, you take out a “lease” on the application’s image and
start downloading it to the device and using it.

As a sort of reference case, I have a [newly installed Ubuntu virtual
machine](virtual-machine-setup.md) which consumes 11 gigabytes on disk
and is configured with 2 gibibytes of RAM, and I’m using a 20Mbps
Argentine internet connection at the moment.  Downloading these 13
gigabytes of data to a local machine would take about an hour and a
half.

However, you might be able to reduce this time in a number of ways:

1. You might be able to **demand-page** it to some extent, prioritizing
   the transfer of blocks of the memory or disk image that the
   application is blocking on to download first.  This way you might
   be able to use the virtual machine considerably earlier than an
   hour and a half.  (Of course, some kind of prefetch strategy could
   make demand-paging work a lot better.)

2. You might be able to **cache** it.  If the image is organized as a
   (materialized) Merkle tree, then you can download only the blocks
   that aren’t already present locally; moreover, the rsync algorithm
   (ideally with a zsync-like precomputed index) may offer further
   benefits, allowing typical filesystem changes to be transferred
   very rapidly.  Merkle-tree storage implies indexing the blocks of
   the image by a secure hash, which will automatically deduplicate
   them.  After the base Ubuntu install, for example, I installed a
   bunch of development tools and some software projects in a derived
   image, which used only 2 gigabytes in the derived disk image,
   which would take only 13 minutes to transmit.  (Probably some
   things changed in RAM, too, but I don’t have a good way to measure
   them.)

3. You can **compress** the transferred data, using an algorithm like
   gzip (LZ77) or LZSS.  For example, the 11-gigabyte Ubuntu install
   mentioned above gzips to only 4.2 gigabytes, reducing the initial
   setup time to about 40 minutes (including RAM); the 2-gigabyte
   derived image — the deltas to set up a development environment
   — compresses to 1.03 gigabytes, about seven minutes.

4. You can **run the app on the server** while the transfer is
   happening, transmitting screen images and input events over the
   network in parallel with the streaming of the memory image.  This
   of course means that the image is being partly invalidated while
   it’s being transferred, but this measure may be enough to reduce
   the pause by orders of magnitude.  If the app’s rate of
   invalidating pages is lower than the available bandwidth, for a
   long enough period of time for the previously invalidated pages to
   be transferred, the pause will be reduced to zero.

5. You can **get a faster internet connection**.  For example, if you
   have a 400Mbps connection instead of 20Mbps, the same transfer
   would take 5 minutes instead of an hour and a half.

6. You can **use less storage**.  For example, the Emacs process I’m
   typing this note in has a virtual memory size of 308 megabytes, of
   which 16 megabytes is resident; the 308 megabytes includes all of
   its shared libraries and Lisp code, though 250 megabytes of it is
   two mappings of the 125MB
   /usr/share/icons/hicolor/icon-theme.cache, which hasn’t changed in
   eight months and gzips to only 17 megabytes.  So a full app
   snapshot of this Emacs process would take two minutes rather than
   an hour and a half, or 30 seconds with gzip, and if only the 16
   megabytes were needed, it would take only six seconds.

7. You can **flush caches**.  Most in-memory application state is not
   vital and can be regenerated from other, more compact state — a
   decompressed image in BGRA can be regenerated from its JPEG, for
   example.  If the application can be notified to flush caches in
   preparation for checkpointing, then everything gets easier.  It
   probably isn’t necessary to have a special case for the Linux disk
   cache, though, since indexing by hash takes care of that already.

Leases, stealing, and committing
--------------------------------

How would you get state back onto the home server?  Unless you want to
require every app to be written in terms of CRDTs or event sourcing,
you need some kind of concurrency control, specifically mutual
exclusion.

The most reasonable solution is to acquire a **lease**, a time-limited
lock, on the application state you’re “checking out”.  So when you
start snarfing the dirty pages into your tablet, the tablet might
acquire a three-hour lease it renews every hour.  As long as it holds
that lease, any attempt to check out the application state on another
machine will fail, telling you to close it on your tablet first.  When
you close the application on the tablet, it releases its lease, so the lease
terminates earlier than the three-hour deadline, which simply serves
as a timeout to permit automatic recovery in case of device failure.

Periodically the tablet checkpoints the local state of the application
locally, then (if still connected to the internet) begins streaming
the dirty pages of that checkpoint back up to the server as a possible
future commit.  Once that checkpoint finishes streaming, it optionally
commits it on the server, then makes a new checkpoint and starts
streaming that one to the server.  Since the checkpoint isn’t modified
while it’s streaming, the streaming process is guaranteed to finish in
finite time, however slow the connection, although it might take a
long time on a slow connection.  The state that is committed is always
a consistent checkpoint from a single point in time, but it may be
somewhat out of date.

So if your local device fails, you only lose the last few minutes of
work; the rest, up to the last committed checkpoint, is saved on the
server.

This approach permits internet-disconnected operation for a limited
period of time as well, for which purpose you might want a longer
lease, maybe a day or two up to a month or two.  This poses the
problem of what happens if the device owning the checkout is lost,
stolen, or broken; in such a case you will want to **steal** the lease,
so any state on the lost device becomes **orphaned** and cannot be committed to
the original application image, though it can perhaps be committed as
a new image that branched from the original.

“Read-only checkouts” are also useful: checkouts of the application
image that succeed even if a lease is outstanding, acquire no lease
themselves, and cannot commit, used for consulting data in the app
without making (persistent) modifications to it.

Committing from an expired or orphaned lease or a read-only checkout
can be allowed if no other commits have happened since the checkout
and there is no lease outstanding.

Reasons for migrating
---------------------

The main reasons for wanting to migrate a running app to the computer
in your hand are (a) interaction latency, (b) disconnected operation,
and (c) experimentation you might not want to deploy.  The main
reasons for wanting to migrate it to a server are (a) greater compute
resources, (b) higher bandwidth and lower latency to the rest of the
internet, (c) making it available to interact with other people, and
(d) potential recovery from device failure.

So you could, for example, check out a website onto your netbook,
modify some things about its setup while disconnected, test it locally
to ensure it’s working as desired, then commit it to the server once
you reconnect to the internet.  Or you could stream checkpoints of
your digital audio workstation to your home server so that if it
breaks or gets stolen you suffer minimal interruption to your work.
Or you could interactively edit a 3-D scene on your laptop in Blender,
then migrate your Blender session to your rendering cluster to run
faster overnight.  Or your could periodically checkpoint a
long-running compute job on a cluster, on individual machines or
cluster-wide, saving the snapshots to a different machine in order to
recover from partial failures.

An interesting special case is where the device you’re running on
doesn’t have enough space for a whole snapshot, so it needs to
occasionally demand-page in bits of the image while it’s running.
This could make it feasible to run memory-hungry applications like
Slack on machines with relatively little RAM, although swapping over
the network like that can be slow.

Another sort of special case is where the “home server” is just a
local disk, and effectively you’re just implementing checkpointing and
software suspend.  This should give you quicker boots (if you can
circumvent slow BIOS/UEFI/Linux anyway) and, if you run out of
battery, you’ll recover to the latest consistent checkpoint when you
come back up.

More generally, you can have topologies other than a simple
client-server topology; you could write out checkpoints to a local
disk, which is streaming them to another server elsewhere, or you
could have a distributed net of servers for block storage, leases, and
commits, with some kind of quorum system for things that require
consensus.  Multiple clients on the same LAN can promiscuously share
new blocks that might be useful for migration.  And so on.

Security issues
---------------

Cloning a machine containing secrets, including entropy pool data, can
lead to the inadvertent disclosure of secrets with many cryptosystems;
for example, it can lead to nonce reuse, or the computation of
multiple RSA keys containing common factors.  It would be advisable to
consider any such random numbers to be nonrandom after a checkpoint.
Moreover, host-based security measures like retry limits and sleeps
between wrong-password retries are entirely circumvented if the
attacker can snapshot and replicate the host, but of course in that
case the attacker owns the hardware and the game is over anyway.

The migrated state is subject to corruption from whatever host it’s
been migrated to, so in effect the running application is trusting
every host that has ever committed to it in the past; any of them can
have inserted arbitrary malicious code into the imge.  In theory a
defender might be able to detect this, but in practice probably would
not.

In the form described above, the application state is also entirely
vulnerable to the server; a malicious server can steal information and
make arbitrary modifications to it.  If you were willing to give up
the possibility of executing applications on the server, you could
reduce this vulnerability to some extent by signing and encrypting the
application state on the clients, perhaps even limiting the server’s
powers to mere denials of service; you’d have to be careful about
replay attacks, and it might not be possible to stop them entirely,
and of course the amount and pattern of encrypted data blocks read and
written might provide a malicious server with access to information we
would prefer to conceal from it.

Concrete implementation approaches
----------------------------------

QEMU’s CLI has “stop”, “cont”, “savevm” and “loadvm” commands that
might be a sufficient hook to implement such a system, reducing the
problem to a problem of synchronizing qcow2 images (or, possibly,
snapshots thereof).  QEMU also has a live migration feature (I don’t
know how this works) and the ability to create a “copy-on-read” image
with a remote “backing file”, which is awfully similar to the features
described above; however, VM snapshotted states from the backing file
are not available in the derived image.

QEMU now has a machine type called “microvm” intended for booting
single-application virtual machines.

I wrote about [a user-level virtual-memory system that would
facilitate this kind of copy-on-write thing](segments-and-blocks.md).

WebAssembly is an obvious implementation technology to try, both in
that the client apps could be web browsers and in that WebAssembly
runtimes are likely to support the kinds of isolation and snapshotting
that would be useful for this kind of thing, as well as often being
more manageable than entire Linux installations.

Docker of course is commonly used for running single (server)
applications in an isolated environment, and it extensively uses
copy-on-write to keep its disk space usage somewhat manageable.  A
typical Docker image using Alpine Linux might be 700 MB, five minutes.  (I thought
it was a lot smaller, but the ones I have here are that big.)  It
would be interesting to try replicating Docker instances around.
