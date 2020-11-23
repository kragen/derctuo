I watched a video demo of a new Visual Studio Code plugin for Autodesk
Fusion 360 "postprocessor" plugins, which are evidently written in JS.
These emit G-Code — I think they might take G-Code as input, but I'm
not sure.  The video demo showed a feature similar to Bret Victor's
famous tree-landscape-drawing demo, in which by clicking on a piece of
G-Code in the output, you would immediately jump to the line of source
code that emitted it.

This is a super cool feature, and I realized that I really want this
for debugging printfs in general: I want to be able to click on a
debug log message and get a stack trace of the program as it was at
the moment the log message was emitted.  Perl's Carp module has
provided such a facility in a purely textual form for a long time, and
Purify and Valgrind have provided it for memory allocation, but I want
to be able to do it for any output, especially debugging output.

Moreover, I especially want to be able to do this for immediate-mode
GUIs; I want to be able to jump from a GUI control on the screen into
the code that painted it, and see the stack trace as it was at the
moment that control was painted.  This is actually maybe *easier* to
provide than the purely textual version of this feature, because if
the feature is still there on the screen when I click on it, and the
program is still running, that stack is *actually running* at the
moment that my click is delivered.

Delivering this functionality for screenshots and recorded sessions
would be harder.
