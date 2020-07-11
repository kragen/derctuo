I set up a virtual machine this week using the virtual-machine emulator
QEMU with KVM under Ubuntu 20.04.

Objectives
----------

I want to have a cloud development server.  A problem with this in the
past has been upgrades: if I don’t upgrade the machine’s software, it
gets out of date and progressively more painful to do things on.  But
when I do upgrade it, I’m at risk of the machine not booting any more,
perhaps requiring a crash cart to visit it, or even plugging the disks
into another machine (that still boots) to recover their data.

Amazon AWS allows you to snapshot an EC2 volume before trying an
upgrade, so you can roll it back if things go badly.  Other
virtualization and paravirtualization systems have similar
capabilities.  The simplest solution is just to use QEMU running under
a popular system with good support; Ubuntu 20.04 is supported until
2025, for example.  Then the “hypervisor” operating system
installed on the physical hardware can remain relatively
untouched by whatever development activities I’m doing, while the
guests can evolve at will.

It would also be nice to be able to use a sandbox with some chance of
containing potential attacks to a single more or less disposable
virtual machine.

Also, there are some experiments I’ve been wanting to try for a while
[involving incremental snapshots of virtual
machines](migrating-app-snapshots.md), and this might be a nice
stepping stone.

Initial setup procedure
-----------------------

In order to get KVM working, first we had to enable “Virtualization
Technology” in the Dell PowerEdge R610 machine’s BIOS; it was disabled
by default, as indicated by the `kvm-ok` command, although enabled by
default in Ubuntu 20.04’s kernel and present in the CPU, which
`/proc/cpuinfo` says is an “Intel(R) Xeon(R) CPU E5649 @ 2.53GHz”.

I was having a hard time setting up Debian inside QEMU, so I snarfed
the Ubuntu install ISO (SHA256
e5b72e9cfe20988991c9cd87bde43c0b691e3b67b01f76d23f8150615883ce11)
instead.  This is a reconstruction of what would have had the right
effect (I mistakenly used QED instead; see “Escaping QED” below):

    qemu-img create -f qcow2 ubuntu-base.qcow2 32G
    kvm -hda ubuntu-base.qcow2 -cdrom Downloads/ubuntu-20.04-desktop-amd64.iso -m 2G

`kvm` is the command installed by the `qemu-kvm` package which is just
equivalent to `qemu-system-x86_64 -enable-kvm`.  (Older versions of
`qemu-kvm` were actually a separate branch of QEMU I think, but it’s
still more convenient to invoke it this way.)

At first I made the mistake of making the disk too small; Ubuntu 20.04
claims to need at least 8.6 GB to install, and in fact used 8.8 GB.
(The QCOW2 format is allocate-on-write,
so even though the virtual disk is 32 GB, the `ubuntu-base.qcow2` file
it’s stored in is only 8.8 GB, since it’s mostly unused.) Also,
QEMU’s default memory size turns out to be 128MiB, which is too small, and Ubuntu’s
installer “reported” this fact by displaying a blank text-mode screen
with a blinking cursor and never doing anything else; `-m 2G` or
something is needed.

At first I was having trouble with keyboard focus in QEMU, which I
think may be a matter of using the obsolete and buggy window manager
`wm2`; I worked around this by running QEMU with `-vnc :2`.  QEMU by
default has no authentication on its VNC interface; rather than fixing
this (see below about the options to fix that) I just packet-filtered VNC
on the machine hosting QEMU
and, for good measure, X-Windows too:

    iptables -A INPUT -s 127.0.0.0/24 -p tcp --dport 5900:6100 -j ACCEPT
    iptables -A INPUT -s 192.168.0.0/24 -p tcp --dport 5900:6100 -j ACCEPT
    iptables -A INPUT -p tcp --dport 5900:6100 -j REJECT

(A little additional work was needed to get this to take effect at
every boot.)

This is a little dodgy given that network traffic from the virtual
machine itself appears to come from localhost, since it’s using the
`user` networking type (Slirp), so different virtual machines have
free rein to connect to VNC and X servers.

To connect remotely to the server from outside its local network, I’m
tunneling over `ssh`, which works pretty well:

    ssh -C -L 5902:localhost:5902 server

That way I can run `xvncviewer :2` on the machine I’m sshing from, and
`ssh` encrypts and compresses the data over the network, as well as
(implicitly) authenticating me by making the connection to the VNC
server come from localhost.

Once I had Ubuntu installed, I could run the virtual machine without
the CD-ROM:

    kvm -hda ubuntu-base.qcow2 -m 2G

But rather than running directly from there, I used it as a base for
cloning further copy-on-write disk images, which is a feature of the
QCOW, QCOW2, and QED virtual disk formats:

    qemu-img create -b ubuntu-base.qcow2 -f qcow2 ubuntu-dev0.qcow2
    qemu-img create -b ubuntu-base.qcow2 -f qcow2 ubuntu-dev1.qcow2
    chmod 444 ubuntu-base.qcow2

Now ubuntu-base.qcow2 is what Proxmox calls a “template”: you can’t
start it but you can create and start clones of it.

And I wrote a script to launch virtual machines with these cloned disk
images:

    $ cat dev0
    #!/bin/sh
    kvm -hda ubuntu-dev0.qcow2 -smp 12 -m 2G "$@"

This approach allows me to clone new virgin virtual disks at a cost of
some 200 kB (plus whatever is used thereafter, typically tens of
megabytes to gigabytes) and 250 milliseconds.  That way I won’t have
to install Ubuntu again.

Escaping QED
------------

Initially I used the deprecated disk image format QED (`-f qed`)
because I misunderstood the QEMU documentation to be saying that it
had some extra features; to fix it, I did this:

    qemu-img convert ubuntu-base.qed -O qcow2 ubuntu-base.qcow2

This took 4-6 minutes and shrank the file to 8.8 GB.  Then I 
needed to recreate the dev child image and reinstall the things
that I had installed in it previously.

Making a backed QCOW2 image is actually significantly slower than doing it with QED,
but not enough to matter for my
purposes; doing this with QED took 10–11 milliseconds:

    $ time qemu-img create -b ubuntu-base.qcow2 -f qcow2 ubuntu-dev0.qcow2
    Formatting 'ubuntu-dev0.qcow2', fmt=qcow2 size=34359738368 backing_file=ubuntu-base.qcow2 cluster_size=65536 lazy_refcounts=off refcount_bits=16

    real    0m0.244s

The resulting derived file is only 197kB; after spending ten minutes
installing stuff in it, it’s 1 GB.

Interestingly, both QCOW2 and QED can use a file in a different format
or even accessed over HTTP as the backing file, so I could put that
base image (or the QED one) up on a web site and remotely lazily clone
it!

Recovering disk space used by deleted VM snapshots
--------------------------------------------------

After I used `savevm` a couple of times, `qemu-img` reported, at one
point:

    $ qemu-img info ubuntu-dev0.qcow2 
    image: ubuntu-dev0.qcow2
    file format: qcow2
    virtual size: 32 GiB (34359738368 bytes)
    disk size: 5.67 GiB
    cluster_size: 65536
    backing file: ubuntu-base.qcow2
    Snapshot list:
    ID        TAG                 VM SIZE                DATE       VM CLOCK
    1         tetris1             1.5 GiB 2020-07-10 16:40:17   00:01:43.207
    2         ready               1.5 GiB 2020-07-10 16:59:52   00:11:43.959
    Format specific information:
        compat: 1.1
        lazy refcounts: false
        refcount bits: 16
        corrupt: false

So it seems like the VM-state snapshots show up as disk-state
snapshots.  I have deleted them:

    qemu-img snapshot ubuntu-dev0.qcow2 -d tetris1
    qemu-img snapshot ubuntu-dev0.qcow2 -d ready

But this does not reduce the size of the QCOW2 file all the way back
down; `du -h` and `qemu-img info` show that it's still occupying 3.9
GB of real space, and its file size in `ls -lh` is still 5.7 GB (so
it’s somewhat sparse).

I thought maybe
`qemu-img convert` might solve the problem, but it seems that
`qemu-img convert` produces an image without a backing file — so it’s
ten gigs.  It turns out that the way to avoid this is using `qemu-img
rebase`, as explained in the qemu-img man page:

    qemu-img create -b ubuntu-dev0.qcow2 -f qcow2 ubuntu-dev0-copy.qcow2 # 92 ms
    qemu-img rebase -b ubuntu-base.qcow2 ubuntu-dev0-copy.qcow2  # 76773 ms

This produces a 2.4-gigabyte copy which `qemu-img compare` reports is
identical to `ubuntu-dev0.qcow2`.  (I'm not sure but I think I have
about 2.4 GB of devtools stuff installed in this image, above and
beyond what’s in the base image.)

Results
-------

So far everything seems reasonably okay except that screen redraws are
painfully slow.

In single-CPU user-level compute performance, QEMU with KVM seems to
only cost on the order of 5%, if anything: `./fib 40` inside QEMU takes 632–663 ms,
while on the host machine it takes 619–641 ms.  However, the host
machine has 12 CPUs with hyperthreading, thus 24 “CPUs”, while the
QEMU-emulated machine initially had only a single virtual CPU.

It turns out QEMU has an `-smp` flag that’s just off by default.
Running `./dev0 -smp 12` (or later adding `-smp 12` in the `dev0`
script) and building
[Yeso](https://gitlab.com/kragen/bubbleos/tree/master/yeso) with
`make` takes 9.3–10.2 seconds.  `make -j 12`, to run up to 12
compilation processes in parallel when possible, takes 1.8–2.2
seconds; that’s more than a 5× speedup.  On the host machine, the
corresponding numbers are 7.4–8.4 seconds and 1.41–1.45 seconds,
suggesting that QEMU’s overhead for system things like file I/O and
process management is more like 30%.  And on the host machine `make -j
30` is even faster, at 1.35–1.40 seconds, but unsurprisingly provides
no additional speedup on the 12-CPU virtual machine.

Over my high-latency internet connection to the server, graphical user
interfaces are a bit slow, perhaps in part because of bandwidth
limits; repainting a full 1024×768 virtual screen takes 5–15 seconds.
However, browsers typically load pages a lot faster; they’re
just slower to scroll.  It might be worthwhile trying XPra or Spice to
see if I can get faster screen updates, or just using ssh and/or Mosh
when possible.

Running with `-vnc :1` I can get a console in my terminal window with
`-monitor stdio`.  This is apparently how to use the `set_password`
command to require a password on the VNC server (required with `-vnc
:1,password` supposedly).  (SASL is also an authentication option.)
Also apparently `-vnc localhost:1` would also only allow connections
from localhost, though without any real authentication.

By using [`savevm
tetris1`](https://www.qemu.org/docs/master/qemu-doc.html#vm_005fsnapshots)
at the monitor prompt `(qemu) ` I can save a virtual machine image
that I can later revive with `kvm ... -loadvm tetris1`, thus returning
to a particular point in the Tetris game I was playing.  Doing this
bloats the .qcow2 file from 1 GB to 2.6 GB, presumably with a RAM
image, and takes about 15 seconds, during which time the VM is paused,
which is pretty disruptive.  Reloading from this image is, I think,
faster than saving (or booting), but it still takes 15 seconds to repaint my screen
over this slow internet connection.

A lazy clone of a disk image (QCOW2 at least) doesn’t share the
snapshots of its backing file.  Presumably I could clone an
already-booted virtual machine (with the booted state in a VM
snapshot) by `cp foo.qcow2 bar.qcow2`.

XPra
----

I decided to try XPra to see if I could get a more usable remote
display for graphical things than VNC, which was too slow.  On my
outdated Linux Mint laptop, I installed XPra 0.15.8 (from 2015):

    sudo apt install xpra python-rencode python3-rencode python-gtkglext1

The last three of which were because Xpra complained about missing
Python libraries; I think probably python3-rencode was unnecessary,
since XPra on this laptop is running in Python 2.  On the Ubuntu 20.04
server, I installed XPra 3.0.6:

    sudo apt install xpra

Then I was able to launch a remote xterm displaying on my local
display via

    xpra start ssh:serverhost --start=xterm --remote-xpra=xpra

and later reattach to the session containing the xterm with

    xpra attach ssh:serverhost --remote-xpra=xpra

Within the xterm I could then run

    ./dev0

in order to launch the QEMU KVM virtual machine as described
previously.

There’s still highly noticeable lag, but it seems dramatically more
usable than VNC.  And VNC had more trouble with my keymapping.  XPra
is reportedly using peaks of up to about 16 megabits per second.  My
initial impression of XPra: *this is fucking awesome*.

It might be more reasonable to run XPra within the guest instead of on
the host (that way copy and paste would work, for example),
but this was an easier way to get started, and it allows me
to handle the guest bootup process as well.

Unknowns to probe/things to try
-------------------------------

What’s the most reasonable way to enable ssh into these virtual
machines?  I’d need to disable password authentication and do some
kind of port forwarding.
By default QEMU does its networking with Slirp,
but it can alternatively use TUN/TAP or
L2TPv3.  There used to be a `-redir tcp:2222::22` option that looks
like it will work, which I think is now spelled `-net
user,hostfwd=tcp::2222-:22`.

How about Mosh?

Is there some way to save VM state snapshots
in a copy-on-write way so that I
can journal aggregated machine state changes out over a network for
point-in-time recovery?  Even cooler would be if I could unfreeze from
such a snapshot when an ssh connection came in.

Can I get Ubuntu or Debian [to boot in QEMU with KVM with
`-nographic`](https://askubuntu.com/questions/924913/how-to-get-to-the-grub-menu-at-boot-time-using-serial-console/1110209#1110209)?

What’s the easiest way to do copy-paste in and out of QEMU, when not
using ssh?  Am I better off using
[spice](https://wiki.archlinux.org/index.php/QEMU#SPICE) ([see
also](https://www.linux-kvm.org/page/SPICE)) or curses?  [Apparently
Spice makes it
easier](https://askubuntu.com/questions/858649/how-can-i-copypaste-from-the-host-to-a-kvm-guest).

Is my window manager really what’s at fault in the keyboard focus
problem?

How insecure is KVM?

How about accessing files on the guest’s filesystem?  There are
`-fsdev` and `-virtfs` flags to QEMU, but I’m not sure what they do.

Is there an advantage to [kvm -M
pc-q35-focal](https://discourse.ubuntu.com/t/virtualization-qemu/11523)?
The default is pc-i440fx-focal.

What do Bonnie++ and lmbench think?  Does using the virtio block
controller instead of emulated IDE help?  [The Proxmox dox
say](https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines#_emulated_devices_and_paravirtualized_devices):

> It is highly recommended to use the virtio devices whenever you can,
> as they provide a big performance improvement. Using the virtio
> generic disk controller versus an emulated IDE controller will
> double the sequential write throughput, as measured with
> bonnie++(8). Using the virtio network interface can deliver up to
> three times the throughput of an emulated Intel E1000 network card,
> as measured with iperf(1). [1]
