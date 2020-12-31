Last night I watched some of the demo videos of Jonathan Edwards's
most recent investigations in Subtext: one in which the I/O stream is
a filtered view of the program's execution (hiding everything that
isn't an input or an output); one in which sequences of steps from the
edit history can get packaged up into formulas; and one in which you
incrementally build up a set of example scenarios as a sort of test
suite as you run the program.

It occurred to me that programming in the debugger, Minsky-style, is
kind of like programming by demonstration.  You have some values in
the registers, and you add an instruction to the program, and continue
the program.  The instruction runs, but then you run off the end of
the program and fall back into the debugger, with the results in the
registers.  Now you add another instruction and run that.  Perhaps you
add a conditional jump which isn't taken this time, and restart the
program from the beginning with a different input, and see where you
end up: off the end of the program, or off the conditional jump into
nowhere, at which point you can start programming that case.

This is not a style of interaction that modern programming tools
support very well, except for spreadsheets.  Bret Victor and Jonathan
Edwards, among others, have done a number of explorations of what a
less limited programming environment in that direction might look
like.

