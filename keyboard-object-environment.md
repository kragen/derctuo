I'm going through a list of part numbers and snarfing datasheets for
them.  I've written a googling script for this, but I still end up
copying and pasting URLs, typing "wget " or "links " or "mupdf ",
tab-completing filenames that were just output, and so on.

I feel like the ideal thing here would be to have my keyboard *focus*
be on some *object*, such as an URL or a downloaded file, and have a
menu of keystrokes for *operations* or for changing the focus.  This
doesn't have to be modal in the Bravo sense — the command keystrokes
could all be control keys, for example.

The operations I'm currently going through are:

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

The idea here is that you are standing on top of a linear "operand
stack" of objects, some of which correspond to filesystem files, and
you can use keyboard commands to invoke operations provided by the
objects, or to navigate and manipulate your environment.  Generally
objects are not destroyed, just moved to a second "discard stack" from
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
something that doesn't handle text, the environment will create a new
string object for you in this case.  If your focus is on a string and
you want to create a new blank string to type into, you can press
Enter.

For more efficient structuring, you can create a new "frame" by typing
^O.  Frames can contain other objects.  They support some additional
key commands:

- Alt-^F or alt-rightarrow: make the frame gobble up the thing below
  it on the operand stack, moving it from that operand stack to the
  bottom of the frame's operand stack.

- Alt-^B or alt-leftarrow: move the item at the bottom of the frame's
  operand stack into the current operand stack, under it.

These two provide a convenient way to gather together a group of
related objects to move them together to some other place and deposit
them there.  But frames also contain a whole new operand stack and
discard stack, a whole universe within a universe, allowing you to
enter them and navigate around within them:

- TAB: cycle the frame's display on the stack between one-line,
  expanded, and fully expanded versions

- ^F or rightarrow: *enter* the frame to abide within it a while.

The environment also has a ^B or leftarrow command to get out of the
current frame, returning to wherever it lives.

There is an important distinction here between modeless universal
commands available everywhere — ^B, ^N, ^P, alt-^N, alt-^P, ^O, and
typing text — and per-object-type operations only available on a
particular object.

So, so far we have, apparently, an outliner.  But each object type can
both affect our view of the stack and provide additional operations.
The simplest case of this is provided by strings, which implicitly
highlight matches for their contents when you are focused on them,
slightly graying out other parts of the stacks that don't match them.
But they also have an operation that hides non-matching parts
entirely, toggled by the TAB key!