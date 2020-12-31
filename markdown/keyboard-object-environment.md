I’m going through a list of part numbers and snarfing datasheets for
them.  I’ve written a googling script for this, but I still end up
copying and pasting URLs, typing “wget ” or “links ” or “mupdf ”,
tab-completing filenames that were just output, and so on.

I feel like the ideal thing here would be to have my keyboard *focus*
be on some *object*, such as an URL or a downloaded file, and have a
menu of keystrokes for *operations* or for changing the focus.  This
doesn’t have to be modal in the Bravo sense — the command keystrokes
could all be control keys, for example.

The operations I’m currently going through are:

- search an existing collection of datasheets for a part number
- google datasheets for a part number
- look at the SERP results
- select an URL from the SERP
- run `links` or `wget` on it
- navigate in `links` and maybe save a file
- delete the resulting file
- open it in `mupdf` (faster) or `xpdf` (shows outlines but often has
  trouble with non-ASCII) or `evince` (handles Chinese in outlines
  better, and prefetches)
- rename it

It would be really nice to have an environment where I could implement
new object types, or add new operations to existing object types, and
have them immediately visible and instantly invocable.

For integrating existing programs like `links` or `catdoc` into the
environment, it would be useful to run them inside a container so that
the environment can observe what new files they create and reify them
in the UI.  But it might be good enough to just rescan the directory
after they exit.

(You could implement such an environment with multitouch too, of
course.)

A nested stack-based keyboard interaction design
------------------------------------------------

The idea here is that you are standing on top of a linear “operand
stack” of objects, some of which correspond to filesystem files, and
you can use keyboard commands to invoke operations provided by the
objects, or to navigate and manipulate your environment.  Generally
objects are not destroyed, just moved to a second “discard stack” from
which you can easily recover them; nor are they duplicated.

The discard stack is displayed upside down above the operand stack.

The basic navigation commands are:

- ^N or downarrow: discard the top of stack by moving it to the top of
  the discard stack

- alt-^N or alt-downarrow: discard the object *under* the top of the
  stack by moving it to the top of the discard stack

- uparrow or ^P: recover the object at the top of the discard stack by
  moving it to the top of the operand stack

- alt-uparrow or alt-^P: recover the object at the top of the discard
  stack by moving it *under* the top of the operand stack.

It will be seen that these four operations are sufficient to move your
focus to any object and to permute the objects arbitrarily, though
inconveniently.  You can also type to create new string objects or
edit the text in existing string objects; if your focus is on
something that doesn’t handle text, the environment will create a new
string object for you in this case.  If your focus is on a string and
you want to create a new blank string to type into, you can press
Enter.

For more efficient structuring, you can create a new “frame” by typing
^O.  Frames can contain other objects.  They support some additional
key commands:

- Alt-^F or alt-rightarrow: make the frame gobble up the thing below
  it on the operand stack, moving it from that operand stack to the
  bottom of the frame’s operand stack.

- Alt-^B or alt-leftarrow: move the item at the bottom of the frame’s
  operand stack into the current operand stack, under it.

These two provide a convenient way to gather together a group of
related objects to move them together to some other place and deposit
them there.  But frames also contain a whole new operand stack and
discard stack, a whole universe within a universe, allowing you to
enter them and navigate around within them:

- TAB: cycle the frame’s display on the stack between one-line,
  expanded, and fully expanded versions

- ^F or rightarrow: *enter* the frame to abide within it a while.

The environment also has a ^B or leftarrow command to get out of the
current frame, returning to wherever it lives.

There is an important distinction here between modeless universal
commands available everywhere — ^B, ^N, ^P, alt-^N, alt-^P, ^O, typing
text, and a few others to be mentioned later — and per-object-type
operations only available on a particular kind of object such as a
frame.

So, so far we have, apparently, an outliner.  But each object type can
both affect our view of the stack and provide additional operations.
The simplest case of this is provided by strings, which implicitly
highlight matches for their contents when you are focused on them,
slightly graying out other parts of the stacks that don’t match them.
But they also have an operation that hides non-matching parts
entirely, toggled by the TAB key!  And, when this is activated, the
^N, ^P, alt-^N, and alt-^P commands leap past the hidden items as if
they weren’t even there; so by creating a search string and activating
it with TAB, you can fold space and ride the string as a swift vessel
to your destination.

(Hmm, at this point I think it’s probably better to have a separate
“kill frame” accessed with ^K and ^Y, so that you don’t leave the
search string beached at your destination.  Then our two stacks merge
into a single buffer that you move around in, rather than being two
stacks.)

Such navigation is facilitated by further commands attached to alt-0,
alt-1, and so on up to alt-9, displayed next to applicable items.  In
the case of a search, these just move your focus to the specified
item, but other types of objects can instead use them for other
purposes.

The datasheet example doesn’t involve ever combining two objects
together in a single operation, so it is not a good source of such
operations; my original example in Dercuano was numerical calculation.
In the datasheet example, though, it’s just fanout: string search term
→ Google SERP list → choose a link → open, rename, or delete the file.
You might choose the same link more than once, so you probably don’t
want the links to get consumed when you invoke operations on them
(like “open in `links`”).

Actually, maybe there is an opportunity: represent the SERP as a
sequence of 14 or so items, the first few of which are “wget”,
“links”, “firefox”, and “close SERP”, and the others of which are the
various URLs:

    wget <-
    links
    firefox
    close SERP
    http://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-0896-FPGA-AT40K05-10-20-40-Datasheet.pdf
    https://www.microchip.com/AT40K20
    https://datasheetspdf.com/datasheet/AT40K20.html
    https://datasheetspdf.com/pdf/865245/ATMELCorporation/AT40K20/1
    https://www.mouser.com/ProductDetail/Microchip-Technology-Atmel/AT40K20-2BQJ?qs=Nb%252BVlYz%2F1KKCyOZ25ZvByg%3D%3D
    https://www.digikey.com/number/en/microchip-technology/150/AT40K20/9619
    http://www.digchip.com/datasheets/parts/datasheet/054/AT40K20-2AQC.php
    https://www.trustedparts.com/en/part/microchip/AT40K20-2BQJ
    https://www.application-datasheet.com/pdf/microchip-technology/519015/at40k20-2aji.html

So initially your focus will be on, say, “wget”, and you can just
select a link with alt-0 or whatever to invoke “wget” on the first
link, creating another item for the file which you can then open with,
say, “mupdf” or “xpdf”.  The “wget” object isn’t just a string; it
knows that only URLs are acceptable fodder for it.

But it might be just as reasonable to give the URL objects methods for
wget, links, etc., that are invoked with separate keys.  That’s not a
reasonable alternative for numerical calculation, though.

Numerical calculation with units
--------------------------------

Suppose you have some length on top of the stack, 3.1 meters or
something, and you invoke its multiplication operator, which pushes a
curried multiply onto the stack.  Well, you can multiply 3.1 meters by
any quantity: 5 newtons, or 2 meters, or 14.5 square centimeters, or
just 3.  But not by, say, “your mom”.  So all the quantities (and none
of the strings) on the stack are highlighted and given numbers; you
can either use alt-5 or whatever to select the thing you want to
multiply by, or you can navigate the multiply up or down to the
appropriate place, or in and out of frames, or whatever.  And then you
can invoke it with whatever operand, the multiply operator disappears,
and you get a product.

But if you invoke the addition operator instead, then only other
lengths are fair game for addition, and 5 newtons and 14.5 square
centimeters are grayed out.  Just like your mom.

One thing I really like about this design is that you can very
reasonably pop up menus of other things to do with your quantity.  You
can see a length represented in all the common length units, for
example, and if you select one the list of units disappears and the
displayed representation changes.  And you might reasonably include
actions like changing the plot color or numerical display format of a
value, as well.

The focused element having the opportunity to change the display of
everything else offers a substantial set of possible flexibility.  In
a numerical calculation, one obvious thing to include is the gradient,
but also you can imagine various kinds of objects that change the view
in various ways, perhaps previewing effects they could have if
invoked.

Quasimodal operator keys
------------------------

Hmm, if you really want to minimize calculation keystrokes, you
could make + or - or * or whatever *quasimodal*: when you press it,
the reified operation appears in the buffer and its intended target
is highlighted, and if you want to apply it to something else you
need to select from a menu or start navigating, because if you
don’t, it gets applied to the nearest applicable target as soon as
you release the key.  But I think that’s a *different* interaction
design.

The preview principle mentioned above would suggest that in such a
system, the default second target would have preview operation results
displayed next to it.  So if you have three numbers with the third one
focused, you might have a display like

    -6
     3  +: 7 -: 1 *: 12 /: 1.33
     4

and if you start pressing the \* key, you might see

    -6 0: -24
     3     12
     4      *

and if you then release \* without pressing 0, then you’d send both
the 3 and the 4 to the kill list and end up looking at

    -6  +: 6 -: 18 *: -72 /: -½              3
    12                                       4

If you instead pressed 0 before releasing \*, 3 survives, -6 and 4 get
killed, and you end up looking at

      3  +: -21 -: -27 *: -72 /: -8         -6
    -24                                      4

If instead of having a kill list you just have the two stacks as I
initially suggested, the “killed” numbers might just move down and be
unhighlighted:

      3  +: -21 -: -27 *: -72 /: -8
    -24
     -6
      4

A flat list
-----------

Note that the ^K/^Y formulation suggested above with a separate “kill
frame” eliminates the need to have arbitrary frames and frame
navigation to reorganize things easily; you can just vacuum up all the
items you want to move to a particular part of the list and then truck
them all over there at one in the kill frame.  This is probably
simpler than the outliner and dual-stack approach.

A filesystem
------------

You could imagine applying such a formulation, either with nested
frames or with just a working list and a trashcan list, to the problem
of a filesystem for a small computer such as a pocket
calculator — rather than giving your files names, you might give them
positions in the list.  This might include executable files — which
perhaps can be simply frames, containing lists of steps (as in HP-48
RPL) or lists of methods annotated with control keys.
