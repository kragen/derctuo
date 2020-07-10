I set up a virtual machine today using QEMU with KVM under Ubuntu
20.04.

Initial setup procedure
-----------------------

In order to get KVM working, first we had to enable “Virtualization
Technology” in the Dell PowerEdge R610 machine’s BIOS; it was disabled
by default, as indicated by the `kvm-ok` command, although enabled by
default in Ubuntu 20.04’s kernel and present in the CPU, which
`/proc/cpuinfo` says is an “Intel(R) Xeon(R) CPU E5649 @ 2.53GHz”.

I was having a hard time setting up Debian inside QEMU, so I snarfed
the Ubuntu install ISO instead.

    qemu-img create -f qed ubuntu-base.qed 32G
    kvm -hda ubuntu-base.qed -cdrom Downloads/ubuntu-20.04-desktop-amd64.iso -m 2G

This is using the ISO image with SHA256
e5b72e9cfe20988991c9cd87bde43c0b691e3b67b01f76d23f8150615883ce11.

XXX I should not have used QED; it's a deprecated format

`kvm` is the command installed by the `qemu-kvm` package which, as far
as I can tell, is equivalent to `qemu-system-x86_64 -enable-kvm`.

At first I made the mistake of making the disk too small; Ubuntu 20.04
claims to need at least 8.6 GB to install, and in fact used 11 GB.
(The QED format, added in recent QEMU versions, is allocate-on-write,
so even though the virtual disk is 32 GB, the `ubuntu-base.qed` file
it’s stored in is only 11 GB, since it’s mostly unused.) Also,
whatever QEMU’s default memory size is [128MiB], it’s too small, and Ubuntu’s
installer “reported” this fact by displaying a blank text-mode screen
with a blinking cursor and never doing anything else; `-m 2G` or
something is needed.

At first I was having trouble with keyboard focus in QEMU, which I
think may be a matter of using the obsolete and buggy window manager
`wm2`; I worked around this by running QEMU with `-vnc :2`.  QEMU by
default has no authentication on its VNC interface; rather than fixing
this (there’s maybe an option to fix that?) I just packet-filtered VNC
on the machine hosting QEMU
and, for good measure, X-Windows too:

    iptables -A INPUT -s 127.0.0.0/24 -p tcp --dport 5900:6100 -j ACCEPT
    iptables -A INPUT -s 192.168.0.0/24 -p tcp --dport 5900:6100 -j ACCEPT
    iptables -A INPUT -p tcp --dport 5900:6100 -j REJECT

(A little additional work was needed to get this to take effect at
every boot.)

To connect remotely to the server from outside its local network, I’m
tunneling over `ssh`, which works pretty well:

    ssh -C -L 5902:localhost:5902 server

That way I can run `xvncviewer :2` on the machine I’m sshing from, and
`ssh` encrypts and compresses the data over the network, as well as
(implicitly) authenticating me by making the connection to the VNC
server come from localhost.

Once I had Ubuntu installed, I could run the virtual machine without
the CD-ROM:

    kvm -hda ubuntu-base.qed -m 2G

But rather than running directly from there, I used it as a base for
cloning further copy-on-write disk images, which is a feature of QED:

    qemu-img create -b ubuntu-base.qed -f qed ubuntu-dev0.qed
    qemu-img create -b ubuntu-base.qed -f qed ubuntu-dev1.qed
    chmod 444 ubuntu-base.qed

And I wrote a script to launch virtual machines with these disk
images:

    $ cat dev0
    #!/bin/sh
    kvm -hda ubuntu-dev0.qed -m 2G "$@"

This approach allows me to clone new virgin virtual disks at a cost of
some 320 kB (plus whatever is used thereafter, typically tens of
megabytes to gigabytes) and 10–11 milliseconds.  That way I won’t have
to install Ubuntu again.

Results
-------

In single-CPU user-level compute performance, QEMU with KVM seems to
only cost on the order of 5%: `./fib 40` inside QEMU takes 660–663 ms,
while on the host machine it takes 619–641 ms.  However, the host
machine has 12 CPUs with hyperthreading, thus 24 “CPUs”, while the
QEMU-emulated machine has only a single virtual CPU.

It turns out QEMU has an `-smp` flag that’s just off by default.
Running `./dev0 -smp 12` and building
[Yeso](https://gitlab.com/kragen/bubbleos/tree/master/yeso) with
`make` takes 9.6–10.0 seconds.  `make -j 12`, to run up to 12
compilation processes in parallel when possible, takes 2.0–2.2
seconds; that’s almost a 5× speedup.  On the host machine, the
corresponding numbers are 7.4–8.4 seconds and 1.41–1.45 seconds,
suggesting that QEMU’s overhead for system things like file I/O and
process management is more like 30%.  And on the host machine `make -j
30` is even faster, at 1.35–1.40 seconds, but unsurprisingly provides
no additional speedup on the 12-CPU virtual machine.

Over my high-latency internet connection to the server, graphical user
interfaces are a bit slow, perhaps in part because of bandwidth
limits.  However, browsers typically load pages a lot faster; they’re
just slower to scroll.  It might be worthwhile trying XPra or Spice to
see if I can get faster screen updates, or just using ssh and/or Mosh
when possible.

Unknowns to probe/things to try
-------------------------------

What’s the most reasonable way to enable ssh into these virtual
machines?  I’d need to disable password authentication and do some
kind of port forwarding; I forget how QEMU does its networking.  (I
think by default it uses Slirp but can alternatively use TUN/TAP or
L2TPv3?)

How about Mosh?

Can I get QEMU to authenticate VNC connections?  It makes me uneasy to
have them totally open inside the firewall.  (Apparently I can say
`-vnc :2,password` and then set the password “using the
“`set_password`” command in the
`[pcsys_monitor](https://www.qemu.org/docs/master/qemu-doc.html#pcsys_005fmonitor)`.”
 — but I’m not sure how to get to the monitor; typing ^Ah does nothing — and
also apparently
`-vnc localhost:2` will only allow connections from localhost.)

When connected to QEMU over VNC, can I access QEMU’s console to do
things like tell it to shut down?

Can QEMU snapshot the machine RAM state like VirtualBox does, so I can
start new virtual machines without booting them?
[`-loadvm` maybe?](https://www.qemu.org/docs/master/qemu-doc.html#vm_005fsnapshots)
Bonus if there’s some way to do this in a copy-on-write way so that I
can journal aggregated machine state changes out over a network for
point-in-time recovery.  Even cooler would be if I could unfreeze from
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

Is QED really better than QCOW2?  I thought QED introduced the -b base
image thing, but [apparently QCOW2 also supports
it](https://askubuntu.com/questions/884534/how-to-run-ubuntu-desktop-on-qemu).
Also apparently QCOW2 can use a non-QCOW2 image as the base.

Is there an advantage to [kvm -M
pc-q35-focal](https://discourse.ubuntu.com/t/virtualization-qemu/11523)?
The default is pc-i440fx-focal.