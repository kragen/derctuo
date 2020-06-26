An outline of the design process leading up to the Veskeno virtual machine
==========================================================================

> ¿Ves que no?

The primary goal of Derctuo is to present some calculations and
computational simulations in a reproducible fashion, so that it is
possible for other people to build on them.  Unfortunately, and quite
surprisingly, no suitable medium for such things currently
exists — except in the limited sense that bytes and computers are
potentially such a medium.  But a raw sequence of bytes is meaningless
without some kind of interpretation, a “file format”, and as far as I
can tell, no suitable file format currently exists.

“Veskeno” is the name I have adopted for such a file format, which
unfortunately requires the development of a new virtual machine for
reproducible computations.  The reasons for this require some
explanation.

The determinism of mathematics
------------------------------

Consider the polynomial *x*⁴ - *x*³ - 5*x*² - *x* - 6.  We can
reasonably make assertions about it; for example:

1. “As you can see, this polynomial has two real and two imaginary
   roots.”
2. “At *x* = 0, this polynomial’s derivative is -1, and at *x* = 1,
   the derivative is -5.”
3. “For real *x*, this polynomial is bounded below by a constant, but
   not bounded above.”

Moreover, you can compute that, for example, one of the polynomial’s
zeroes is at *x* = -3, while another is at *x* = 2.

Or are they?  Either way, you can calculate what the zeroes are,
although it may not be easy — it’s a matter of objective truth or
falsity.  If I’ve made an error, you can find it, and if I’m correct,
you can verify that.  And you can be sure that anyone else who does
the calculations will get the same answer — regardless of their
cultural background, ethical beliefs, or latitude, regardless of
whether they’re in 02020 CE, 02120 CE, or 12020 CE.  Indeed, any
rational being in any possible universe would get the same results.
Unless they failed to understand or made a mistake.  (Did I make a
mistake above?)

There are a variety of things for which we can make such objective
assertions: which side won a chess game, given all the moves, for
example, or that the word “fire” occurs 559 times in the King James
Version of the Bible, at least if we can agree on which version that
is, and what counts as an occurrence of the word — I omitted
occurrences of “fiery”, but counted words containing “fire”, such as
“firepans” and “firebrands”.

The objective of Veskeno: make software, specifically Derctuo, run as reproducibly as other mathematics
-------------------------------------------------------------------------------------------------------

Since at least Church, Turing, and Gödel, we have a rigorous
mathematical formalization of the notion of an *algorithm*, which is
how we have managed to build digital computers that can be programmed
to execute any algorithm in the first place.  So we know that we can, in
theory, come to the same kind of consensus about the behavior of an
algorithm — in theory any algorithm whatever can be executed on any
computer in the universe, on the same input data, and compute exactly
the same results.  And, again in theory, it does not matter whether
the computation happens in 02020 CE or 02184 CE; the results will be
exactly the same, precisely the same sequence of bits.  In theory,
programs are incapable of nondeterminism, unless the computer
malfunctions.†

In practice, however, we have a very different situation, one prone to
what Konrad Hinsen calls “software collapse”, colloquially known as
“bitrot”: software that works perfectly on one machine or at one time,
but fails to run correctly or even to compile on another machine or at
another time; or it may run but be unusable in practice for one or
another reason.  Occasionally this happens because of changes in the
universe of inputs and outputs — many IBM PC games ran too fast to be
playable on the IBM PC AT, for example, and a user interface designed
for mice may require too much precision to be usable in practice on a
multitouch hand computer — but much more often software collapse
happens because software or hardware dependencies changed their
behavior, so the same program computes different results from the same
inputs.  Often a large body of tacit knowledge, concerning what
changes have happened, must be drawn upon to repair software collapse;
if the maintainers of the codebase are dead or uninterested, repairing
it may be infeasible.

Veskeno’s objective is to put the theory into practice, so that the
algorithmic results in Derctuo are as reproducible as algebraic
results.  This way, it is hoped, the written record of algorithmic
knowledge can engage the kind of ratchet of progress that has
propelled mathematics and the natural sciences forward, so that each
generation of researchers can build on the results of the previous
generation, rather than — as normally happens with
software — reinventing those results from scratch.  And it need not be
dependent on the maintenance of a living tradition, since Veskeno can
be reimplemented from its specification.

We want to have a high degree of assurance that, if a computation has
occurred under Veskeno and the program and input data are available,
we can reproduce the same computational results with a new
implementation of Veskeno, although perhaps more slowly, or, with
luck, more quickly; we want to minimize the chance that a bug in
either the new or especially the old implementation breaks this
reproducibility.

We adopt the following priorities in order to achieve this:

1. The Veskeno specification should be sufficiently strict and
   detailed that, given any Veskeno program and its input data, any
   two correct implementations of Veskeno should produce bitwise
   identical results, unless one or both of them unpredictably fails.
   (See below about predictable and unpredictable failure.)

2. The Veskeno specification should be sufficiently simple that a
   programmer should be able to implement it in an afternoon, given
   only the spec — without having access to running implementations to
   test against.  Moreover, the implementation should be more likely
   correct, as defined above, than incorrect, once it passes all the
   tests in the spec — even if implemented on hardware that would be
   extremely unusual in 02020 CE, such as a decimal or ternary
   computer.

3. A straightforward one-afternoon Veskeno implementation should be
   efficient and full-featured enough to run practical
   computations — for example, to run 1980s video-games at playable
   speeds, on mainstream 02020 CE personal computing hardware such as
   a Samsung Galaxy A10.

4. It should be practical to generate working code for Veskeno without
   unreasonable space overheads, compilation-time costs, or headaches.
   However, since the objective is to spend hundreds or thousands of
   hours writing Derctuo so that someone can write a Veskeno virtual
   machine on which to run it in six hours or so, it’s worth trading
   off 100 hours of effort programming *for* Veskeno to save even a
   single hour of effort writing a Veskeno implementation.

5. The damned thing needs to get done in a month or two and have
   working software on it at that point.

A consequence of priorities #1 and #2 is that it should *usually* be
impossible for a malicious attacker, upon examining two simple
implementations of Veskeno that pass the tests in the spec, to
construct a Veskeno program and input dataset that runs successfully
on both implementations but produces different results.

This set of priorities leads to a very unusual set of design
tradeoffs, one so alien to modern mainstream virtual machine design
that the comment was heard, “I feel like this is designing a weapon or
something.”

There are varying levels of abstraction at which we could such
reproducibility could be guaranteed; Veskeno takes the simplest
approach, of prescribing reproducibility at the level of individual
CPU instructions, which compose into reproducible macroscopic
computations.

****

† Conventionally these results are stated for batch-mode algorithms
which run for some finite period of time and then halt with a result,
but it’s straightforward to extend them to interactive processes — the
batch-mode algorithm takes as input a previous state and an input
event (which may be simply that there is no input of interest to
report) and eventually produces a new state and perhaps some output.
(Extending this statement to multithreaded programs and
interrupt-driven I/O is less straightforward but in principle possible
by treating these new sources of nondeterminism as more kinds of input
events.)

A note about hardware performance
---------------------------------

A Galaxy A10 (30 million sold in 02019) has 2 GiB of RAM and eight
Cortex-A cores running at 1.35 to 1.6 GHz, capable in all of perhaps
20 billion 64-bit multiply-accumulate operations per second, plus
a Mali-G71 MP2 GPU, which I think is about 50 gigaflops on two cores.
A 1980s video-game might have 1 MiB of RAM and execute a million
16-bit multiply-accumulates per second.

So the *throughput* performance overhead
budget here is about a factor of 2048 in space and a factor of some
131072 in time, though of course greater speed and less overhead would
be desirable, since it would make much more elaborate computations
reproducible.  Typical straightforward low-level virtual machines can
achieve time overhead factors of 3–20 and space overhead of 1.1–4, but
we don’t have to come close to that; moreover Veskeno is serial,
imposing [another factor of 16–64
of overhead on the throughput](vector-vm.md) unless
some kind of parallelism is possible.  This leaves us some 128× of
performance headroom.

A typical 1980s video-game might run on a 6502 like the 1.79MHz one in
a Nintendo; [a multiply routine for the 6502] takes 130 CPU clock
cycles to multiply 8 bits by 8 bits and get a 16-bit result, while a
version using a table of squares takes 79–83 cycles.  At the 6502's
max of 3 MHz this might give us 38000 8-bit multiplies per second, or
only about 22000 on the Nintendo, working out to about 5000 16-bit
multiplies per second; typically, though, 6502-based video-games
paired the CPU with sprite hardware to do compositing of video-game
characters onto a background, thus reducing the load on the CPU.  By
the end of the 1980s, though, some video-games ran on CPUs that were
some 256 times faster, obviating the need for sprite hardware.

[a multiply routine for the 6502]: https://www.lysator.liu.se/~nisse/misc/6502-mul.html

On the other hand, for faithfully reproducing the "feel" of human
interactions with existing computer systems, simulating the *analog*
behavior of computer hardware often demands significant computational
work.  XXX at masswerk.at has reproduced "Spacewar!", perhaps the
first video-game done on a computer; he reports that the most
difficult and time-consuming part was accurately reproducing the color
change and exponential decay behavior of the PDP-1's display.
Accurately simulating analog video artifacts like chroma subsampling,
NTSC artifact colors, ghosting, blur, ringing, noise, and
pincushioning, as the Apple2 XScreenSaver module does, can use an
unboundedly large amount of digital computation.

Predictable and unpredictable failure
-------------------------------------

Above, I said, “any two correct implementations of Veskeno should
produce bitwise identical results, unless one or both of them fails.”
Why is failure an option, and what kinds of failure handling can we
have?

The simplest and most unavoidable kind of unpredictable failure is
that an implementation, although correct, runs too slowly to be worth
waiting until it finishes.  Perhaps a given computation requires an
hour of CPU time on an efficient implementation of Veskeno; an
inefficient implementation might be 1000 times slower and run on a CPU
that is 8 times slower, thus requiring 8000 hours, about 11 months.
Such a computation is almost certain to be aborted before completion
unless its results are of great interest and using the computer for
other tasks is of little interest.

Dynamic memory allocation failure is another kind of unpredictable
failure which, although it is not unavoidable, may be preferable to
the cost of avoiding it.  It is straightforward to write a program
such that it can handle any amount of data that would fit into virtual
memory — 4 gibibytes in the case of a 32-bit machine — while being
able to handle smaller amounts of data, such as 100 kibibytes, in much
smaller amounts of memory.  We could avoid the possibility of runtime
dynamic memory allocation failure by preallocating 4 gibibytes, so
that the program will entirely fail to run on machines with only, for
example, a gibibyte of RAM, even if given only 100 kibibytes of input.
This would prevent it from running on, for example, the Samsung Galaxy
A10 mentioned above, since it has only 2 gibibytes.  (Kernel memory
overcommit would have to be turned off to achieve this under Linux,
since otherwise the Veskeno virtual machine process can be OOM-killed
at any time.)

In many environments, it is very difficult to ensure that dynamic
memory allocation failures cannot happen during execution, because
many basic operations of the language can invoke dynamic allocation.
In CPython, for example, even integer arithmetic can invoke dynamic
memory allocation, and it is common even for languages like C to
dynamically allocate function activation records on a stack, although
perhaps this can be avoided during execution of Veskeno.  Attempting
to outlaw Veskeno implementations in such environments would be futile
and probably counterproductive.  Also, Veskeno itself is probably such
an environment: a Veskeno virtual machine interpreter written in
Veskeno can be useful for many things, but unavoidably will have less
memory space available for the program it interprets than it has
itself.

However, for some applications of Veskeno — not those in Derctuo — it
would be desirable to ensure that no such unpredictable failures will
arise, so that a Veskeno-implemented algorithm can be used to, for
example, safely control an antilock braking system, a jet engine, a
milling machine, or a self-balancing scooter.  Typically in these
cases a worst-case execution time is also demanded.  For these cases,
we would need to preallocate all the needed resources.

A third kind of possible failure arises from correctness checks on
operations such as arithmetic, memory access, and I/O.
Conventionally, for example, dividing by zero or dereferencing a null
pointer will raise an exception that can terminate a program or reset
a computer.  On some systems, arithmetic overflows may also raise such
exceptions, the Ariane 5 maiden flight being one notorious example.
For debugging, these exceptions are highly desirable, since they often
point quite directly to the problem in the program, while incorrect
results might easily be overlooked.  They are different from the above
kinds of failures, though, because they are not unpredictable: running
the same program on the same input data will always produce the same
failure — although in many systems the exception happens some time
*after* the actual failure.

What would happen if a Veskeno program had the option to handle
unpredictable failures?  For example, if dynamic memory allocation
sometimes reported failure to the program, or invoked an exception
handler in the program.  Then some executions of the program on the
same input data would see a reported failure, while others would get
success, so their executions would diverge — Veskeno could no longer
guarantee that the results were equivalent.  Even if the results were
marked as “error output” rather than “algorithm results”, since a
failure had happened during the run, people would start relying on
that error output.

So, because unpredictable failure is not deterministic (in terms of
the supposed inputs) recovery from unpredictable failure must be
impossible.  This reasoning does not apply to predictable failures,
and so it is reasonable to include predictable-failure cases in the
Veskeno test suite.

However, even predictable failure cases pose some real difficulty in
reproducibility, because they tend to be very poorly tested.  Ordinary
computations, outside the test suite, will not normally depend on the
behavior of failure cases, so it is easy for a case to slip through
where the virtual machine is supposed to detect a certain failure, but
it fails to do so — a failure to fail, you might say.  Then users will
write programs that depend on the virtual machine’s failure in that
case, probably without knowing it, and their behavior will not be
reproducible on other implementation of Veskeno.

A binary, rather than textual, file format
------------------------------------------

It's reasonable to consider, for example, core Lisp as the canonical
representation for algorithms, by which is meant the usual definitions
of S-expressions, CAR, CDR, CONS, QUOTE, NULL, ATOM, EQUAL, LAMBDA,
some kind of conditional such as COND or IF, and some kind of
recursive construct such as LABELS, LETREC, or global DEFUN; and,
indeed, these constructs have a perfectly well defined deterministic
semantics sufficient to express any computable function.  Moreover, in
any modern high-level language with garbage collection, you can write
an interpreter for it in 30 to 120 lines of code, including the reader
and the printer.

However, when we turn to thinking of testing and failures, many subtle
considerations appear.  LAMBDA and LABELS involve the introduction of
symbols; what is the maximum acceptable length of these symbols?  How
many characters of them are significant --- all of them?  Are
characters counted as bytes (in UTF-8?), as Unicode code points, or as
UTF-16 code units?  Which characters are allowed?  Is comparison
case-insensitive, or, if case-sensitive, is it done in, for example,
Normalization Form D?  Is there a maximum nesting depth to lists, and
what is it?  How about a maximum length?  Is the symbol NIL, or some
other symbol, EQUAL to an empty list, or treated as falsehood in
conditionals?  Is the ASCII tab character treated as whitespace?  How
about vertical tab (^K)?  How does EQUAL handle lists --- does it
treat them as always inequivalent, almost like EQ, or does it compare
their contents, and if so does it have a recursion limit?  Must the
input file end with a linefeed character?  What will the parser do if
an extra right parenthesis is added to the end of the file?  How about
an extra left parenthesis?  If an unused argument is specified as a
nonterminating computation, will the computation succeed or not ---
that is, is evaluation lazy or eager?  Does the answer depend on
circumstances?

If mutable state is added --- as it was immediately to Lisp,
historically speaking --- additional questions become relevant.  What
is the order of evaluation of arguments?

These problems are amplified by the fact that the answers may be
dependent on the invocation context in a poorly specified way.  As an
example, CPython's default recursion limit is 1000 stack levels, which
may give rise to a nesting limit of 333, 500, or 1000 for lists in a
straightforwardly-written recursive-descent parser --- but if that
parser is invoked from a context already nested ten stack levels deep,
these limits instead become 330, 495, or 990.  CPython is unusual in
that it handles stack overflow explicitly by raising an exception;
most current and past language environments instead produce
unpredictable incorrect results or crash at the operating-system
level.

Since most of the Lisp primitives draw their arguments from
potentially infinite domains, such as lists and symbols, which are at
least exponentially large, running exhaustive tests for them is out of
the question.

The depth and richness of these likely sources of implementation bugs
would seem to make the following scenario almost inevitable: Alice
implements Veskeno and builds and tests a program as a Veskeno virtual
machine image in her implementation.  Unbeknownst to Alice, her
program depends on symbols with equal Normalization Form D being
treated as EQUAL.  Bob, perhaps three centuries later, implements
Veskeno and attempts to run Alice's virtual machine image.  It
produces different results than it did for Alice.  Bob concludes that
Alice (RIP) was a superstitious fool whose reported results cannot be
trusted, or perhaps a fraud.

This is precisely the scenario Veskeno is intended to prevent.
Veskeno priority #2 says:

> ... the implementation should be more likely correct, as defined
> above, than incorrect, once it passes all the tests in the spec ...

Some of these problems are specific to Lisp and would not be present
with, for example, a textual assembly-language format, but others are
generic problems of most or all textual formats.  And the advantages
of textual formats seem to primarily redound to the benefit of the
person writing a file in them, not to the implementor of a complete,
correct interpreter of the file format.  As the priorities say, "it’s
worth trading off 100 hours of effort programming *for* Veskeno to
save even a single hour of effort writing a Veskeno implementation."
Consequently, a binary file format seems far more likely to be able to
achieve Veskeno's aims.

An untyped 32-bit register machine with mod-2³² wraparound
----------------------------------------------------------

The Veskeno virtual machine has 16 CPU registers and a RAM array;
programs using a stack store the stack in the RAM.  To ease compiling
existing C code for Veskeno, the registers are 32 bits, despite the
hassles that entails in languages like Java or on 16-bit hardware; it
poses no difficulty for Veskeno implementations in languages like C.

The only arithmetic operations it offers are addition and subtraction,
which behave mod 2³² as you would expect.

One great drawback of 32-bit arithmetic is that its input space is of
size 2<sup>64</sup>; as a result, exhaustive testing of an arithmetic
operation would take half a million CPU years at Veskeno's 1-MIPS
performance target.  Most programmers today cannot spend half a
million CPU years on an afternoon project because they do not have
hundreds of millions of CPUs available, nor even hundreds of CPUs; it
is plausible that this parlous situation of poverty will continue for
some time.

### The fibterp spike ###

As a simple experiment to get a handle on software complexity and
interpretive slowdown, I hacked together the following minimal
simulator for such a machine, together with a dumb Fibonacci program
for it; this took 96 minutes and 119 lines of C, 21 of which are the
dumb Fibonacci program in a sort of assembly language.  This virtual
machine has 11 instructions and word-addressed memory, but I think
Veskeno itself will have more like 16 instructions and byte-addressed
memory.

    /* XIS: simple little RISCy bytecode interpreter as a sort of quick spike
     * to see how fast or slow it goes.  Answer: about 20× slower than
     * GCC on the same machine.
     */

    #include <stdio.h>
    #include <stdint.h>
    #include <stdlib.h>
    #include <string.h>


    typedef uint32_t u32;

    typedef struct {
      u32 reg[16];
      u32 *mem;
    } machine;

    enum opcode { insn_push = 0x21, insn_pop, insn_lit16, insn_low16, insn_add, insn_sub,
           insn_jl, insn_halt, insn_call, insn_ret, insn_mov };

    static inline int
    src_reg(u32 insn)
    {
      return (insn >> 20) & 0xf;
    }

    static inline int
    dst_reg(u32 insn)
    {
      return (insn >> 16) & 0xf;
    }

    #define IF break; case
    #define ELSE break; default
    #define rSP 15
    #define SP reg[rSP]
    #define rPC 14
    #define PC reg[rPC]

    int interpret(machine *m, int a, int b, int c, int d)
    {
      m->reg[0] = a;
      m->reg[1] = b;
      m->reg[2] = c;
      m->reg[3] = d;
      for (;;) {
        u32 dest, insn = m->mem[m->PC++];
        enum opcode op = (insn & 0xff000000u) >> 24;
        switch(op) {
        IF insn_push:
          m->SP--;
          m->mem[m->SP] = m->reg[src_reg(insn)];
        IF insn_pop:
          m->reg[dst_reg(insn)] = m->mem[m->SP];
          m->SP++;
        IF insn_lit16:
          m->reg[dst_reg(insn)] = (insn & 0xffff) | (insn & 0x8000 ? 0xffff0000u : 0);
        IF insn_low16:
          m->reg[dst_reg(insn)] <<= 16;
          m->reg[dst_reg(insn)] |= insn & 0xffff;
          abort();  // untested code
        IF insn_add:
          m->reg[dst_reg(insn)] += m->reg[src_reg(insn)];
        IF insn_sub:
          m->reg[dst_reg(insn)] -= m->reg[src_reg(insn)];
        IF insn_jl:
          if (m->reg[src_reg(insn)] & (1 << 31)) {
            m->PC += (insn & 0xffff) | (insn & 0x8000 ? 0xffff0000u : 0);
          }
        IF insn_halt:
          return m->reg[0];
        IF insn_call:
          dest = m->PC + ((insn & 0xffff) | (insn & 0x8000 ? 0xffff0000 : 0));
          m->SP--;
          m->mem[m->SP] = m->PC;
          m->PC = dest;
        IF insn_ret:
          m->PC = m->mem[m->SP];
          m->SP++;
        IF insn_mov:
          m->reg[dst_reg(insn)] = m->reg[src_reg(insn)];
        ELSE:
          abort();                  /* invalid instruction */
        }
      }
    }

    /* assemble register-register instruction */
    #define a_rr(n, s, d) ((insn_##n << 24) | ((s) << 20) | ((d) << 16))
    /* assemble register-dest instruction */
    #define a_rd(n, d) a_rr(n, 0, (d))
    /* assemble register-source instruction */
    #define a_rs(n, s) a_rr(n, (s), 0)

    #define a_k16(n, r, k) ((insn_##n << 24) | ((r) << 16) | ((k) & 0xFfFf))
    #define a_jl(s, off)  ((insn_jl << 24) | ((s) << 20) | ((off) & 0xFfFf))
    #define a_call(off)   ((insn_call << 24) | ((off) & 0xFfFf))
    #define a_ret         (insn_ret << 24)
    #define a_halt        (insn_halt << 24)

    /* dumb fibonacci: if r0 < 2 then 1 else fib(r0 - 1) + fib(r0 - 2) */
    u32 program[] = {
      a_call(1),                 /* call fib */
      a_halt,
      a_rr(mov, 0, 1),           /* fib: r1 := r0 */
      a_k16(lit16, 2, 2),        /* r2 := 2 */
      a_rr(sub, 2, 1),           /* r1 -= r2 */
      a_jl(1, 13),               /* if r1 < 0, go forward 13 insns */
      a_rs(push, 0),             /* push r0 */
      a_k16(lit16, 3, 1),        /* r3 := 1 */
      a_rr(sub, 3, 0),           /* r0 -= r3 */
      a_call(-8),                /* call fib */
      a_rd(pop, 1),              /* pop input r0 into r1 */
      a_rs(push, 0),             /* save return value from recursive call */
      a_k16(lit16, 3, 2),        /* r3 := 2 */
      a_rr(mov, 1, 0),           /* r0 := r1 */
      a_rr(sub, 3, 0),           /* r0 -= r3 */
      a_call(-14),               /* call fib */
      a_rd(pop, 1),              /* pop saved return value into r1 */
      a_rr(add, 1, 0),           /* r0 += r1 */
      a_ret,
      a_k16(lit16, 0, 1),        /* r0 := 1 */
      a_ret,
    };

    int main(int argc, char **argv)
    {
      int n = argc > 1 ? atoi(argv[1]) : 6;
      u32 mem[1024];
      for (int i = 0; i < 1024; i++) mem[i] = 0xdeafbeadu;
      memcpy(&mem[128], program, sizeof(program));
      machine m = { .mem = mem };
      for (int i = 0; i < 16; i++) m.reg[i] = 0xbadfadu;
      m.PC = 128;
      m.SP = 1024;
      int result = interpret(&m, n, 0xfeedbead, 0xfeedbead, 0xfeedbead);
      printf("%d\n", result);
      return 0;
    }

Disassembly shows that GCC compiles the switch with a jump table.  (I
admit I spent another half hour after those 96 minutes looking to see
why it was so slow...)

However, an important caveat here: because this virtual machine
implementation does not bounds-check memory accesses, it fails to be
deterministic.

This program is about 20× slower than native code on my amd64 OoO
laptop and about 40× slower on my i386 Atom in-order netbook.

In theory, someone implementing Veskeno will not have to write and
debug the Fibonacci program and other test programs as they are
writing their virtual machine, much less revise the definitions of the
instructions as they go; instead they can, hopefully, assume that the
instruction set is adequate, the test cases are correct, and any bugs
are in their interpreter.  This should speed up their programming.  In
the past when I’ve implemented simple virtual machines such as Chifir
and Brainfuck, it’s taken me under an hour.  (But, well, my Chifir
implementation had a bug I didn’t notice for months, and it might
still have others.)

Fixed- or variable-length instructions
--------------------------------------

Variable-length instructions are more space-efficient --- the usual
reason for them, irrelevant here --- and make it easy to include
immediate constants of the full width of a register, thus avoiding the
lit16/low16 hack in XIS, the RISCy spike above.

Fixed-length instructions have other advantages.  They can make it
impossible to represent invalid program-counter values, which would
otherwise be a potential source of divergent behavior among
implementations.  They facilitate conditional-skip instructions, which
permit the decoupling of conditional types (equality versus ordering)
from jump types (direct or indirect).  By making them extremely wide,
as Chifir does, they too can contain register-sized immediate
contents.  And they facilitate having an opcode field of less than a
full byte, which reduces the number of tests needed for invalid
opcodes.

As with variable-length instructions, the most important advantage of
fixed-length instructions in hardware is irrelevant for Veskeno: that
they enormously simplify pipelined instruction decoding.

No floating point
-----------------

The Veskeno virtual machine provides no floating-point operations,
despite the importance of floating-point math for numerical algorithms
and the importance of reproducibility for these algorithms.  Instead,
floating-point operations are provided by libraries written in
Veskeno’s instruction set, despite the heavy performance cost, so that
they will have the same behavior on all Veskeno implementations.
Three reasons for this are Gen gradual underflow, `-ffloat-store`, and
FMA.

IEEE 754 standardizes the behavior of the basic floating-point
operations — addition, subtraction, multiplication, division, and
square root — to provide bit-exact results.  Given this, it would be
reasonable to expect that all modern machines would produce identical
results when executing a floating-point algorithm consisting of only
these operations.  However, although it would be reasonable, it would
be wrong.

One aspect of IEEE 754 is the handling of underflow — when numbers
become too small to represent in normalized form, it is specified that
they start losing bits of precision, which continues down to the
smallest nonzero float.  Currently, Intel’s “Gen” GPUs do not
implement this, because it is slow.  Therefore it is not reasonable to
assume that all future hardware will implement gradual underflow
correctly.

GCC has a `-ffloat-store` flag for use with math coprocessor
instructions for the 80387, 68881, and similar chips.  The 80387 and
its compatible descendents, included in every 386-compatible processor
since the Pentium, always internally use 80-bit extended precision.
This means that the results of a sequence of floating-point operations
depends on whether intermediate results are rounded to 32 or 64 bits
to be stored in memory or remain entirely inside the 80-bit register
set, which in turn depends on how effective the optimizer is at
register allocation.  This can, for example, cause some successive
approximation algorithms to loop infinitely.  `-ffloat-store` requires
them to *always* be stored in memory, despite the ensuing dramatic
loss of performance, in order to guarantee deterministic behavior.

Even with the above caveats, some might wonder if the problem is
limited only to hardware that is sort of sketchy, like Gen, or
obsolete, like 32-bit Intel processors.  But in fact a similar, though
subtler, issue arose just in the last few years, with a new “fused
multiply-add” or “FMA” instruction on 64-bit processors, which can
often preserve an extra bit of precision.  This means that the results
of an operation can differ in the least significant bit depending on
whether the compiler’s optimizer was successful at employing FMA on a
given program.

It must be anticipated that Veskeno virtual machine implementations
will be compiled by such compilers.  For Veskeno, the above-described
level of nondeterminism is absolutely intolerable, even merely FMA, so
taking advantage of hardware floating point is not an option.

Exhaustive testing is desirable but probably too slow in the target scenario
----------------------------------------------------------------------------

Single-operand arithmetic instructions are feasible to test
exhaustively; dual-operand instructions less so.  Consider this Python
program:

    #!/usr/bin/python3
    import hashlib

    def add16(a, b):
        return (a + b) & 0xFfFf

    def test_add16():
        h = hashlib.sha256()
        for a in range(1<<16):
            for b in range(1<<16):
                s = add16(a, b)
                h.update(bytes([s & 0xff, s >> 8]))
            if not ((a+1) & 0xf):
                print(a)

        return h.hexdigest()

    if __name__ == '__main__':
        print(test_add16())

This eventually produces the output:

    ca284820199ced0d15c967098f8ffc59e583a8b4120375b09ef1da4366786ca0

This amounts to a compact summary of the overall behavior of the
`add16` function; if a different function produced the same hash, we
could be reasonably confident that its behavior on 16-bit unsigned
numbers was the same as add16's.  And by using Merkle trees we could
detect deviations without finishing the whole test, and, more
important, localize them in particular parts of the input.  (A
cross-cutting Hamming-code-like hashing strategy would permit pinpoint
localization: with 33 hashes for different subsets of these
2<sup>32</sup> test cases --- one for odd-numbered test cases, one for
test cases whose ordinal number is odd when divided by 2 rounding
downward, and so on --- we can easily determine which case is failing
if only one is.)

But this test takes 13 hours and 49 minutes of CPU time to produce
this output on this netbook, thus testing only some 86000 addition
operations per second.  CPython3 on this netbook is pretty close to
Veskeno's target performance of a million multiply-accumulates per
second, although they are 32-bit rather than 16-bit.

It's conceivable that this test could be optimized by up to about an
order of magnitude, but not by two orders of magnitude; and it's more
likely that a similar test in Veskeno would be *much slower*, because
SHA-256 isn't a basic operation like addition.  The corresponding
exhaustive test for a two-operand 32-bit math operation would require
ten orders of magnitude more computation; as mentioned before,
2<sup>64</sup> microseconds is some 585 millennia.

So exhaustive testing of, say, 32-bit addition, is probably not
feasible at the target performance level within the target six-hour
timeframe.  Even exhaustive testing of 32-bit negation would take
hours.  Instead, randomized tests are probably a better fit.

This is not to say that exhaustive testing has no role, just that
faster kinds of testing are needed.

No vector-valued registers
--------------------------

Numpy can typically easily achieve about 20% of C performance on
mainstream hardware today, despite the slowness of the CPython
interpreter, because the inner loops are in C.  [One design considered
for Veskeno](vector-vm.md) used vector-valued registers and RAM — each register or
memory cell could hold a vector of very large size, and Veskeno would
provide SIMD instructions like Numpy’s operations.  Thus the
interpretive overhead of a simple bytecode interpreter loop would be
amortized over larger numbers of fundamental operations, increasing
the speed of Veskeno programs.

The plan is currently not to take this direction, for three reasons:

1. This would create the possibility of thus allocating unpredictable
   amounts of memory in a way hidden from the Veskeno program itself,
   making it impossible to guarantee failure-free execution.

2. The number of distinct SIMD instructions required seems like it is
   probably too large to implement — and, especially, debug — in an
   afternoon.

3. Crude estimation suggests that a straightforward interpreter
   without any such tricks will be more than fast enough to satisfy
   the priorities as described above: 8–32× is a typical interpretive
   slowdown, and Veskeno is aimed at an interpretive slowdown of
   131072× or less.

Not counted here is the serial-computation slowdown, which [is
estimated](vector-vm.md) at 32×.  Above it is estimated that a Samsung
Galaxy A10, for example, can do about 70 billion multiply-accumulate
operations per second, but single-threaded unvectorized code on it
won't get more than about 1.6 billion, 44 times slower; out-of-order
processors with more execution units close the gap a little.  It would
not be surprising for a virtual machine that exploits such data
parallelism to exceed the speed of optimized single-threaded
unvectorized C.

Possible coarse-grained parallelism
-----------------------------------

There is nothing in principle that prevents Veskeno from providing a
"spawn" facility to run a "child" Veskeno computation, given a program
and input data, and such a facility would be very useful for writing
an automatic Veskeno test suite.  If several such concurrent
computations can be run, this might make it possible to gain back a
parallelism factor of some 8 or so on most current hardware, and
probably much larger factors in the future.  Such a facility is
potentially risky, though; it would need to be subject to a number of
restrictions:

1. Although it could report *predictable* failures in the child to the
   parent --- out-of-bounds memory accesses, for example --- it could
   not be allowed to report unpredictable failures such as running out
   of memory.  Unpredictable failures could be handled by
   automatically retrying or by propagating the failure to the parent,
   killing it as well.
2. It probably needs to be impossible to interact with an incomplete
   child computation in order to ensure determinism.  For example, the
   ability to inquire whether a child computation was still running,
   or had already completed, would probably violate determinism.  Any
   attempt to access the results of the child computation before the
   child's completion must transparently block until the child is
   complete.
3. The interface must be simple enough to implement --- correctly! ---
   as part of the same afternoon as the rest of Veskeno.

More elaborate kinds of interaction could in principle be specified;
for example, the parent computation could be provided with facilities
to single-step the child, examine its memory space while
single-stepping, and so on, as long as this did not provide it with
any information about unpredictable failures, machine load, and so on.
But such a facility would probably be more complex both to specify and
to implement than all of the rest of Veskeno, and at any rate it can
be provided less efficiently within Veskeno, without any special
effort from the virtual machine implementor, by a metacircular Veskeno
interpreter.

Multiplication and division?
----------------------------

Perhaps Veskeno should have a multiplication
instruction or instructions.  Most modern processors have a
single-cycle multiplier, and replacing that with a subroutine call is
a heavy performance penalty for programs that do a lot of
multiplication, on the order of 32× to 64×.

However, multiplication can and often does overflow (a whole word’s
worth of bits rather than just one), requiring separate instructions
for the low and high word of the results,
and signed and unsigned
multiplication are different; so supporting multiplication is not as
low-risk as supporting addition or subtraction.

Veskeno probably should not have a division instruction for several
reasons: signed and unsigned integer division are not the same, it’s
ambiguous which way the correct result of negative signed division
should round (quotient toward negative infinity or toward zero?),
division by zero is potentially a predictable failure, and division is
typically slow anyway, so the impact of not using the hardware integer
division instruction is less severe — both because the gap in
performance will be smaller than for multiplication and because
programs are already written to avoid division in hot loops whenever
possible.

I think probably the right choice is to omit multiplication from an
initial Protoveskeno and see how far we get, then possibly add
multiplication instructions if the lack is a sufficiently large
performance loss.

Instruction-set translation
---------------------------

Rather than writing a C compiler backend to target Veskeno, it seems
that binary translation from an existing instruction set which already
has good compiler support may be the best approach.  Supporting 64–128
distinct instructions may be enough, perhaps even using very simple
techniques that in effect simulate the registers and flags of the
target processor.

I/O operations and determinism
------------------------------

PGP and GnuPG have historically used I/O operations to generate
cryptographically random key bits: for example, by measuring the
latency of electromechanical disk requests, which are influenced by
turbulence inside the disk drive, they can produce a reliably
different set of numbers on every execution; another approach is by
measuring the timing of the user's keystrokes.  The objective is that,
if you ask PGP to generate keypairs on two occasions and type the same
input at it to the best of your human ability, you will still generate
two different *and unpredictable* private keys.  (Modern operating
systems provide this facility at a systemwide level using
/dev/urandom, so that randomness gathered before GnuPG or OpenSSH
starts can still provide them with unpredictable secret bits.)

So we can conclude that providing a program with the ability to read
the current time, or to measure the time between inputs, can allow it
to defeat any efforts at guaranteeing reproducible behavior.  On the
other hand, interactive applications like video-games generally must
have time-dependent behavior: the Space Invaders and their bombs must
continue moving at a consistent speed even if the player is not
providing any new input; and when they do provide input, the results
are in general dependent on *when* that input is provided.  Moving
Pac-Man to the left for one second does not have the same results as
moving Pac-Man to the left for two seconds, and so on.

How can these requirements be reconciled?

As explained above about how programs are incapable of nondeterminism
in theory:

> Conventionally these results are stated for batch-mode algorithms
> which run for some finite period of time and then halt with a
> result, but it’s straightforward to extend them to interactive
> processes — the batch-mode algorithm takes as input a previous state
> and an input event (which may be simply that there is no input of
> interest to report) and eventually produces a new state and perhaps
> some output.

We could take this approach in Veskeno: run a *noninteractive*
computation in Veskeno, starting from a snapshot of some previous
state, and ending with a new state snapshot, part of which might be,
for example, a screen image and some samples of audio to output, and
another part of which might specify handlers to run for future input
events, maybe including timeout events.  To reproduce a deterministic
sequence of such deterministic computations or explore alternative
histories, it would be sufficient to record the initial state and the
sequence of input events that were delivered, although it might be a
useful accelerant to save snapshots of some intermediate checkpoint
states.

User interaction isn't the only kind of I/O, though.  It's common for
programs to read from and write to a filesystem, for a variety of
reasons.  Doing this synchronously isn't in itself a source of
nondeterminism --- given a frozen filesystem snapshot that is part of
the initial state from which the Veskeno computation proceeds,
presumably the program will always read the same data in response to
the same seek() and read() calls, unless it alters it in between with
a write().  But it is potentially a source of implementation
complexity and bug-proneness.

Some filesystem access happens because programs are handling data that
doesn't fit in their virtual memory.  This might be reading a
100-kilobyte file on a 16-bit machine or writing a 5-gigabyte file on
a 32-bit machine.  For Derctuo, I can avoid this problem by making
Veskeno not 16 bits, and not managing multi-gigabyte datasets.  If
Veskeno is at some point to be pressed into service wrangling
multi-gigabyte datasets, it could be wedged into the model as if it
were user input: instead of terminating the computation with event
handlers for keystrokes and timeouts, a computation could terminate
with an event handler for a block of data becoming available.  (Or you
could add I/O instructions for doing this to Veskeno; this would make
it no longer compatible with the Veskeno specification, but arguably
so would adding these new kinds of event handlers.)

Input and output data that *does* fit into virtual memory can simply
be put in virtual memory; when a computation terminates, it can do so
with an indication of where its results are to be found in memory.  A
straightforward Veskeno implementation can simply copy such data into
a large byte array, while perhaps a trickier one can take advantage of
mmap() and similar facilities.

Some filesystem access happens to decouple the environment in which a
program runs from what the program does.  For example, I have the file
/usr/lib/python3.4/encodings/mac_greek.py on this netbook.  If a
program does not access this file, or enumerate the contents of the
directory /usr/lib/python3.4/encodings, or look at how much space is
left on the disk, its execution will not be affected by this file; but
if I run CPython 3.4 and type `b'\xce'.decode('mac_greek')`, that file
will be loaded and used to map that byte to U+0388.  It's a resource
available upon request, but otherwise unobtrusive.

Usually you can add new files to a Unix filesystem or new environment
variables to a Unix environment without breaking any existing
programs.  This contrasts to, for example, adding new positional
arguments to a function call.  (Adding new fields to a C struct is a
kind of middle ground: it breaks existing *compiled* programs, but not
existing source code, because it's an incompatible change to the ABI
but not the API.)  This kind of decoupling via name-value pairs is a
pervasive pattern for permitting the independent evolution of
different software components.

To a great extent, such decoupling can be provided within a Veskeno
image without any special support from the Veskeno virtual machine: a
"filesystem", a tree of string-indexed blobs, can be built in memory
and accessed via a filesystem-emulation library.  This collapses if
multiple gigabytes of data are needed, but my intent with Derctuo is
to keep the total size of all the data in the image to double-digit
megabytes.

It's common for physical computers to use "memory-mapped I/O": magical
memory addresses which cause things to happen in the physical world
when they are written or even read.  This is costly to provide in
virtual machines in general, because nearly every time memory is read
or written, a check must be made for these magical addresses.  For
Veskeno, it seems like a particularly bad idea, since it would be easy
to omit the necessary replay functionality.  If I/O operations are to
be added to Veskeno computations, they should be added with explicit
IN and OUT instructions.

Instruction counting and metacircular instrumenting compilers
-------------------------------------------------------------

Derctuo talks fairly often about the *efficiency* of possible
algorithms.  Nowadays this is a difficult thing to nail down:
different algorithms may use different amounts of memory, different
numbers of CPU instructions, differently-predictable memory access
patterns, and afford different degrees of vectorization, out-of-order
instruction-level parallelism, SIMT parallelism, and coarse-grained
(multicore) parallelism, as well as having different patterns of
communication between different cores.  As hardware heterogeneity
increases further into the dark-silicon era we are entering, this
already gnarly efficiency landscape is likely to become more complex
rather than simplifying.

But a simple first-order approximation to computational cost is to
count the number of CPU instructions executed by a single-threaded
version of the algorithm.  Given a nailed-down instruction set like
Veskeno's, this number should be as perfectly reproducible as
everything else about a Veskeno computation, and it would probably
only increase Veskeno's complexity by 2--5 lines of code, a simplicity
loss of perhaps 1--5%.  This may be a worthwhile cost to pay.

However, as with single-stepping, this is a facility that can be
provided by a metacircular Veskeno interpreter: a Veskeno program that
executes Veskeno programs.  Veskeno's simple instruction set suggests
that the binary-translation approach used by Valgrind would be an
especially suitable approach.

Memory maps and relocatability
------------------------------

As long as Veskeno programs can access the raw bits of Veskeno memory
addresses, reproducibility requires that those bits not change between
executions and implementations.  Environments like the JVM avoid this
problem by not providing programs with access to those bits, relying
on a relatively elaborate static type system that reliably
distinguishes memory pointers from other data such as characters and
integers.  A less elaborate hybrid system is possible, in which
pointers are loaded into special registers for pointers (or "segments"
or "descriptors") and stored in special memory for pointers, like
KeyKOS's "nodes"; but even such a scheme seems likely to be far more
complex than Veskeno's complexity budget permits.  (Still, see
[Segments and Blocks](segments-and-blocks.md).)

This means, in particular, that if there's a way to change the Veskeno
memory map after startup, for example by mapping in the contents of a
file (or part thereof) or the results of a child computation, it must
happen at a deterministically chosen, well-specified address.  It need
not be insensitive to the previous execution of the computation ---
for example, it could happen at the end of the current data segment
--- but it cannot happen at an address not specified in the Veskeno
specification.

Self-modifying code
-------------------

Veskeno does not need to permit self-modifying code; it could use a
Harvard architecture, for example, like an AVR, and use a
child-spawning facility like that described earlier if it wants to
generate Veskeno code dynamically.  But, if it does permit
self-modifying code, it is essential for its effects to be
deterministic, well-specified, and well-tested; it would not be
acceptable for different Veskeno implementations to handle the same
self-modifications differently.  The simplest solution is to require
that all modifications take effect immediately, even if to the
immediately following instruction.

Related work
------------

### Preservation through emulation, e.g., SIMH ###

### van der Hoeven and Lorie’s UVM ###

### Chifir ###

In [The Cuneiform Tablets of 2015], Long Tien Nguyen and Alan Kay
described their design for a simple archival virtual machine called
Chifir, for which they report having successfully preserved
Smalltalk-72.

They describe their requirements as follows:

> 1. It can be described in a single Letter or A4-sized page using
>    English and diagrams. A “one-pager” has a nice psychological
>    quality of compactness and elegance to it; we were inspired by
>    the half-page Lisp metacircular evaluator in the Lisp 1.5 manual
>    [27].
> 2. It can be implemented in a single afternoon by a reasonably
>    competent programmer.

Implicitly, they also require that it be sufficiently powerful to run
the system they want to preserve.

[My implementation of Chifir] took me an hour of programming and 111
lines of C, but because Nguyen and Kay have not published their
Smalltalk-72 virtual machine image or any other test data for Chifir,
my implementation may very well have bugs; it might take another hour
or more to find and fix all of its bugs.

Chifir is a word-oriented 32-bit three-operand memory-to-memory
machine with very fluffy instruction encoding.  Its 15 instructions
are roughly JMP, JZ, save-return-address, MOV, LD, ST, +, -, *, /, %,
<, NAND, refresh-screen, and read-keyboard; the half-duplex nature of
the read-keyboard instruction makes it impossible to emulate
full-duplex systems like video-games on Chifir.  But Nguyen and Kay
did not intend for Chifir to be universal in the same way that Veskeno
is; they say:

> We think that trying to design a “universal” virtual machine to
> serve as the simple virtual machine is a bad idea, because trying to
> ensure compatibility with the entire design space of computer
> architectures will make the resulting “universal virtual machine”
> very complicated. In our opinion, this is the mistake of van der
> Hoeven et al.’s Universal Virtual Computer for software preservation
> [15]. They tried to make the most general virtual machine they could
> think of, one that could easily emulate all known real computer
> architectures easily. The resulting design [25] has a segmented
> memory model, bit-addressable memory, and an unlimited number of
> registers of unlimited bit length. This Universal Virtual Computer
> requires several dozen pages to be completely specified and
> explained, and requires far more than an afternoon (probably several
> weeks) to be completely implemented.

[The Cuneiform Tablets of 2015]: http://www.vpri.org/pdf/tr2015004_cuneiform.pdf "VPRI TR-2015-004"
[My implementation of Chifir]: https://gitlab.com/kragen/bubbleos/blob/master/yeso/chifir.c
[27]: http://web.cse.ohio-state.edu/~rountev.1/6341/pdf/Manual.pdf "Lisp 1.5 Programmer’s Manual, McCarthy Abrahams Edwards & Hart, 1962, appendix B, pp. 70–71 (78–79 of 116)"

### Lisp ###

Lisp has a simple core --- not quite as simple as SK-combinators or
the λ-calculus, but still pretty simple.  [The basic forms][27] are
COND, LABELS (now normally called `letrec`), LAMBDA, and QUOTE, which
are "special forms", and the regular functions CAR, CDR, CONS, ATOM,
EQUAL, and NULL; these suffice to write a metacircular interpreter for
Lisp or, for example, a normal-order λ-calculus reducer.

Because both CONS and function application implicitly allocate memory,
as does LAMBDA in modern interpretations (where it produces a
closure), it's difficult for Lisp programs to be failure-free --- when
run on a finite machine they can run out of memory and crash.  But, at
least initially, eliminating unpredictable failures is beyond the
scope of Veskeno.

A binary format like various Lisps' FASL formats could both permit
rapid startup and eliminate text-related parsing bugs.

However, the history of Lisp is littered with subtle bugs.  McCarthy's
1959 paper published a Lisp metacircular interpreter that
inadvertently defined Lisp with dynamic scope --- a bug that remained
ossified in Lisp for nearly a quarter century, with workarounds like
FUNARGS --- and contained a few other subtle bugs.  Writing the
following one-pager in Python took an hour for a programmer who has
implemented Lisps more than once before, running into several minor
bugs on the way; bugs may still remain.

    def Eval(sexp, env):
        return (env[sexp] if type(sexp) is str else
                specials[sexp[0]](sexp[1:], env) if type(sexp[0]) is str
                                                and sexp[0] in specials else
                Eval(sexp[0], env)([Eval(arg, env) for arg in sexp[1:]]))

    def evcon(branches, env):
        for q, a in branches:
            if Eval(q, env): return Eval(a, env)

    def evletrec(args, env):
        assignments, body = args[0], args[1]
        env = env.copy()
        for name, a, b in assignments: env[name] = closure(a, b, env)
        return Eval(body, env)

    def closure(args, body, env):
        return lambda vals: Eval(body, augment(env, list(zip(args, vals))))

    def augment(env, nvpairs):
        env = env.copy()
        for n, v in nvpairs: env[n] = v
        return env

    specials = {
        'cond': evcon,
        'letrec': evletrec,
        'lambda': lambda args, env: closure(args[0], args[1], env),
        'quote': lambda args, env: args[0],
    }

    base_env = {
        'car': lambda args: args[0][0],
        'cdr': lambda args: args[0][1:],
        'cons': lambda args: [args[0]] + args[1],
        'atom': lambda args: type(args[0]) is str,
        'null': lambda args: not args[0],
        'equal': lambda args: args[0] == args[1],
        't': True,
    }

    # produces ['b']
    example_prog = ['letrec', [['assoc', ['k', 'kvs'],
                                ['cond', [['equal', 'k', ['car', ['car', 'kvs']]],
                                          ['cdr', ['car', 'kvs']]],
                                         [['null', ['cdr', 'kvs']],
                                          ['quote', []]],
                                         ['t', ['assoc', 'k', ['cdr', 'kvs']]]]]],
                              ['assoc', ['quote', 'y'], ['quote',
                                                         [['x', 'a'],
                                                          ['y', 'b'],
                                                          ['z', 'c']]]]]

    # produces [['X', 'a'], [['X', 'small'], ['X', 'dog']], ['X', 'sat']]
    example2 = ['letrec',
                 [['subst', ['f', 'd'],
                   ['cond', [['atom', 'd'], ['f', 'd']],
                            [['null', 'd'], ['quote', []]],
                            ['t', ['cons', ['subst', 'f', ['car', 'd']],
                                           ['subst', 'f', ['cdr', 'd']]]]]],
                  ['x', [], ['quote', 'X']]],
                ['subst', ['lambda', ['de'], ['cons', ['x'], ['cons', 'de',
                                                              ['quote', []]]]],
                 ['quote', ['a', ['small', 'dog'], 'sat']]]]

    if __name__ == '__main__':
        import cgitb
        cgitb.enable(format='text')
        print(Eval(example2, base_env))

Running Lisp efficiently requires some kind of garbage collection; the
above implementation inherits from Python not only GC but also its
lists, recursive function calls, equality comparison, closures, I/O,
error reporting, and truthiness, and it takes advantage of Python's
dictionaries.  Its behavior on argument-count mismatch is inherited
from Python's `zip`.  It constructs circular data structures, which
old versions of Python would be unable to garbage-collect.  Probably
an implementation in a lower-level language like C would be
considerably more efficient, but would also require implementing from
scratch these Python bequests.  In my experience this tends to take as
long or longer than implementing the semantic core above expressed in
Python.

### Abadi and Cardelli's ς-calculus of objects ###

### The JVM ###

### ActivePapers ###

### Nix and Guix ###

### The Cult of the Bound Variable ###

32-bit unsigned

Darius suggests it's worth looking at the Sandmark contestants' bugs.

### Corewar Redcode ###

Corewar is a game in which a multithreaded processor "MARS" runs two
programs that try to kill each other, alternating instructions.  Like
the Burroughs 5000, MARS tags memory words as instructions or data; a
program that attempts to execute a data word dies.

The textual Redcode assembly language is the standard format for
specifying these programs; there is no binary program format.  The
determinism of MARS is intentionally limited: programs are loaded at
random starting addresses.  (Absent this measure, whichever program
started running first could win by using its first instruction to
store a data word in the other program's first-executed location.)

### Wirth-the-RISC ###

In the 1990s, Wirth became interested in the potential of FPGAs for
realizing processor designs, especially designs simplified so as to be
easy to teach, without losing practicality.  He produced a series of
progressively more complex designs in Verilog, unfortunately called
RISC0, RISC1, RISC2, RISC3, RISC4, and RISC5, and ported the Oberon
system to run on them.  Lacking a better name, I will just call them
"Wirth-the-RISC".

Wirth-the-RISC is admirably simple, with four condition-code flags for
conditional jumps; 16 conditions for jumps (including "always"), which
can optionally be indirect and/or save a return address; 16
register-to-register ALU instructions, some of which have two variants
--- signed versus unsigned MUL, for example, and ADD with or without
carry; load and store instructions with offsets; and, for the RISC5
processor's interrupts, an instruction to enable or disable
interrupts, and an instruction to return from them.  Four of the ALU
instructions are floating-point, though my impression is that the
processor does not rise to the level of being practical for
floating-point work --- it has no double-precision and no square-root
instruction.

The fact that Wirth-the-RISC successfully runs the Oberon GUI is a
testament to the practicality of this design.

### SOD32 ###

### Brainfuck ###

Brainfuck is a virtual machine of Urban Müller's design; it was not
the first of the "esoteric programming languages" (that would be
INTERCAL) --- or even, I think, the second --- but it was in a sense
the one that established esoteric programming languages as a genre,
inspiring the current profusion.  The Brainfuck virtual machine is,
like INTERCAL, deliberately difficult to program in, but unlike
INTERCAL, implementing it is extremely easy.  One day in 2014 I sat
down to implement it from the spec, which I think took about an hour:

    /*
     * Brainfuck interpreter.
     */

    #include <sys/types.h>
    #include <sys/stat.h>
    #include <fcntl.h>
    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>

    int main(int argc, char **argv) {
      char program[10000];
      int fd = open(argv[1], O_RDONLY);
      if (fd < 0) {
        perror(argv[1]);
        return 1;
      }

      int progsize = read(fd, program, sizeof(program));
      close(fd);

      unsigned char memory[30001];
      int pc = 0, mp = 0;
      while (pc < progsize) {
        /* printf("[%d]", pc); */
        /* fflush(stdout); */
        switch (program[pc]) {
        case '>': mp++; pc++; break;
        case '<': mp--; pc++; break;
        case '+': memory[mp]++; pc++; break;
        case '-': memory[mp]--; pc++; break;
        case ',': read(0, &memory[mp], 1); pc++; break;
        case '.': write(1, &memory[mp], 1); pc++; break;
        case '[':
          if (memory[mp]) {
            pc++;
            break;
          }
          int bc = 0;
          do {
            if (program[pc] == '[') bc++;
            if (program[pc] == ']') bc--;
            pc++;
            if (pc >= progsize) {
              fprintf(stderr, "unmatched [\n");
              return 1;
            }
          } while (bc);
          break;
        case ']':
          if (!memory[mp]) {
            pc++;
            break;
          }
          int bbc = 0;
          do {
            if (program[pc] == ']') bbc++;
            if (program[pc] == '[') bbc--;
            pc--;
            if (pc < 0) {
              fprintf(stderr, "unmatched ]\n");
              return 1;
            }
          } while (bbc);
          pc++;
          pc++;
          break;
        default:                    /* comment! */
          pc++;
          break;
        }
      }
      return 0;
    }

After testing some simple examples, I downloaded Linus Åkesson's
implementation of Conway's Game of Life (pbuh, QEPD):

                Linus Akesson presents:
                       The Game Of Life implemented in Brainfuck

           +>>++++[<++++>-]<[<++++++>-]+[<[>>>>+<<<<-]>>>>[<<<<+>>>>>>+<<-]<+
       +++[>++++++++<-]>.[-]<+++[>+++<-]>+[>>.+<<-]>>[-]<<<++[<+++++>-]<.<<[>>>>+
     <<<<-]>>>>[<<<<+>>>>>>+<<-]<<[>>>>.+<<<++++++++++[<[>>+<<-]>>[<<+>>>>>++++++++
     +++<<<-]<[>+<-]>[<+>>>>+<<<-]>>>[>>>>>>>>>>>>+>+<<     <<<<<<<<<<<-]>>>>>>>>>>
    >>[-[>>>>+<<<<-]>[>>>>+<<<<-]>>>]>      >>[<<<+>>  >-    ]<<<[>>+>+<<<-]>[->[<<<
    <+>>>>-]<[<<<  <+>      >>>-]<<<< ]<     ++++++  ++       +[>+++++<-]>>[<<+>>-]<
    <[>---<-]>.[- ]         <<<<<<<<< <      <<<<<< <         -]++++++++++.[-]<-]>>>
    >[-]<[-]+++++           +++[>++++        ++++<     -     ]>--.[-]<,----------[<+
    >-]>>>>>>+<<<<< <     <[>+>>>>>+>[      -]<<<      <<   <<-]>++++++++++>>>>>[[-]
    <<,<<<<<<<->>>> >    >>[<<<<+>>>>-]<<<<[>>>>+      >+<<<<<-]>>>>>----------[<<<<
    <<<<+<[>>>>+<<<      <-]>>>>[<<<<+>>>>>>+<<-      ]>[>-<-]>++++++++++[>+++++++++
    ++<-]<<<<<<[>>>      >+<<<<-]>>>>[<<<<+>>>>>      >+<<-]>>>>[<<->>-]<<++++++++++
    [>+<-]>[>>>>>>>      >>>>>+>+<<<<      <<<<<      <<<<-]>>> >>     >>>>>>>[-[>>>
    >+<<<<-]>[>>>>       +<<<<-]>> >       ]>> >           [<< <        +>>>-]+<<<[>
    >>-<<<-]>[->[<      <<<+>>>>-]         <[ <            < <           <+>>>>-]<<<
    <]<<<<<<<<<<<, [    -]]>]>[-+++        ++               +    +++     ++[>+++++++
    ++++>+++++++++ +    +<<-]>[-[>>>      +<<<-      ]>>>[ <    <<+      >>>>>>>+>+<
    <<<<-]>>>>[-[> >    >>+<<<<-]>[>      >>>+< <    <<-]> >    >]>      >>[<<<+>>>-
    ]<<<[>>+>+<<< -     ]>[->[<<<<+>      >>>-] <    [<<< <    +>>       >>-]<<<<]<<
    <<<<<<[>>>+<< <     -]>>>[<<<+>>      >>>>> +    >+<< <             <<-]<<[>>+<<
    -]>>[<<+>>>>>      >+>+<<<<<-]>>      >>[-[ >    >>>+ <            <<<-]>[>>>>+<
    <<<-]>[>>>>+<      <<<-]>>]>>>[ -    ]<[>+< -    ]<[ -           [<<<<+>>>>-]<<<
    <]<<<<<<<<]<<      <<<<<<<<++++ +    +++++  [   >+++ +    ++++++[<[>>+<<-]>>[<<+
    >>>>>++++++++ +    ++<<<     -] <    [>+<- ]    >[<+ >    >>>+<<<-]>>>[<<<+>>>-]
    <<<[>>>+>>>>  >    +<<<<     <<      <<-]> >    >>>>       >>>[>>+<<-]>>[<<+<+>>
    >-]<<<------ -    -----[     >>      >+<<< -    ]>>>       [<<<+> > >>>>>+>+<<<<
    <-]>>>>[-[>> >    >+<<<<    -] >     [>>>> +    <<<<-       ]>>> ]  >>>[<<<+>>>-
    ]<<<[>>+>+<< <    -]>>>     >>           > >    [<<<+               >>>-]<<<[>>>
    +<<<<<+>>-                  ]>           >     >>>>>[<             <<+>>>-]<<<[>
    >>+<<<<<<<                  <<+         >      >>>>>-]<          <<<<<<[->[<<<<+
    >>>>-]<[<<<<+>>>>-]<<<<]>[<<<<<<    <+>>>      >>>>-]<<<<     <<<<<+++++++++++[>
    >>+<<<-]>>>[<<<+>>>>>>>+>+<<<<<-]>>>>[-[>     >>>+<<<<-]>[>>>>+<<<<-]>>>]>>>[<<<
    +>>>-]<<<[>>+>+<<<-]>>>>>>>[<<<+>>>-]<<<[     >>>+<<<<<+>>-]>>>>>>>[<<<+>>>-]<<<
    [>>>+<<<<<<<<<+>>>>>>-]<<<<<<<[->[< <  <     <+>>>>-]<[<<<<+>>>>-]<<<<]>[<<<<<<<
    +>>>>>>>-]<<<<<<<<<+++++++++++[>>> >        >>>+>+<<<<<<<<-]>>>>>>>[-[>>>>+<<<<-
    ]>[>>>>+<<<<-]>>>]>>>[<<<+>>>-]<<< [       >>+>+<<<-]>>>>>>>[<<<+>>>-]<<<[>>>+<<
    <<<+>>-]>>>>>>>[<<<+>>>-]<<<[>>>+<        <<<<<<<<+>>>>>>-]<<<<<<<[->[<<<<+>>>>-
     ]<[<<<<+>>>>-]<<<<]>[<<<<<<<+>>>>>      >>-]<<<<<<<----[>>>>>>>+<<<<<<<+[>>>>>
     >>-<<<<<<<[-]]<<<<<<<[>>>>>>>>>>>>+>+<<<<<<<<<<<<<-][   lft@df.lth.se   ]>>>>>
       >>>>>>>[-[>>>>+<<<<-]>[>>>>+<<<<-]>[>>>>+<<<<-]>>]>>>[-]<[>+<-]<[-[<<<<+>>
           >>-]<<<<]<<<<<<[-]]<<<<<<<[-]<<<<-]<-]>>>>>>>>>>>[-]<<]<<<<<<<<<<]

            Type for instance "fg" to toggle the cell at row f and column g
                       Hit enter to calculate the next generation
                                     Type q to quit

As with INTERCAL, Brainfuck ignores anything it does not understand,
so the textual comments do not interfere with the execution of the
program.

This was a delightful experience, because by virtue of writing 68
lines of C, I had implemented a virtual machine capable of running any
Brainfuck program, and had transformed the ASCII-art textphile above
into a running implementation of the Game of Life!  In principle, the
C program above could compute any computable function, as long as it
didn't require more than 30001 bytes of memory.

Brainfuck itself, though, is a finger pointing at the moon; it is not
the moon.  It has no subroutine-call mechanism, it cannot run code
generated at runtime, a straightforward implementation of it is
absurdly inefficient, the encoding of its programs is also absurdly
inefficient, and there have been several different incompatible
semantics (for example, for the overflow of a memory location, and of
course for the size of memory), so some Brainfuck programs are
incompatible with some Brainfuck implementations.

Also, the issue of I/O is swept under the rug.  The above Life
implementation is interactive on a terminal; it draws the gameboard
using ASCII art.  You can correct your input errors with backspace
only thanks to the line-editing capabilities provided by default by
the kernel or the C library; by the same token, Brainfuck programs
running in the above C implementation in the same way as Life cannot
provide so much as tab-completion and overstrikes, much less mouse,
key-release, and graphics handling.  To emulate video-games, even with
a sufficiently powerful implementation, Brainfuck would need a mapping
between streams of input and output bytes and the input and output
events of interest; this mapping, too, would need to be standardized
for such an emulation to be portable among implementations.

Here's a sample dialogue with the Life program, using this
implementation of Brainfuck:

     abcdefghij
    a----------
    b----------
    c----------
    d----------
    e----------
    f----------
    g----------
    h----------
    i----------
    j----------
    >de
     abcdefghij
    a----------
    b----------
    c----------
    d----*-----
    e----------
    f----------
    g----------
    h----------
    i----------
    j----------
    >df
     abcdefghij
    a----------
    b----------
    c----------
    d----**----
    e----------
    f----------
    g----------
    h----------
    i----------
    j----------
    >fe
     abcdefghij
    a----------
    b----------
    c----------
    d----**----
    e----------
    f----*-----
    g----------
    h----------
    i----------
    j----------
    >ee
     abcdefghij
    a----------
    b----------
    c----------
    d----**----
    e----*-----
    f----*-----
    g----------
    h----------
    i----------
    j----------
    >ed
     abcdefghij
    a----------
    b----------
    c----------
    d----**----
    e---**-----
    f----*-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----------
    c----------
    d---***----
    e---*------
    f---**-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----------
    c----*-----
    d---**-----
    e--*--*----
    f---**-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----------
    c---**-----
    d---***----
    e--*--*----
    f---**-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----------
    c---*-*----
    d--*--*----
    e--*--*----
    f---**-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----------
    c----*-----
    d--**-**---
    e--*--*----
    f---**-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----------
    c---***----
    d--**-**---
    e--*--**---
    f---**-----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b----*-----
    c--**-**---
    d--*-------
    e--*---*---
    f---***----
    g----------
    h----------
    i----------
    j----------
    >
     abcdefghij
    a----------
    b---***----
    c--****----
    d-**--**---
    e--*-**----
    f---***----
    g----*-----
    h----------
    i----------
    j----------
    >q

These 8 generations of 10×10 Life required 98 CPU seconds on this
netbook (with the Brainfuck implementation compiled with `cc -O5
-fomit-frame-pointer -Wall -std=gnu99` using GCC 4.8.4), illustrating
the efficiency problems of Brainfuck.  I took a couple of hours to
write the following C version of Åkesson's awesome program, which,
compiled the same way, was able to do 80000 generations in 1.424 CPU
seconds, an efficiency difference of some 700k×, suggesting that the
Brainfuck slowdown in this case is about 5 or 6 orders of magnitude.

    #include <stdio.h>

    enum { ww = 10, hh = 10 };

    int board[3][hh][ww];

    /* From the cells in `from`, compute a parallel array with the sum of
       cells above and to the left of that cell, including that cell
       itself.  For example:

        >>> x
        array([[1, 0, 1],
               [0, 2, 1],
               [1, 1, 1]])
        >>> x.cumsum(axis=0).cumsum(axis=1)
        array([[1, 1, 2],
               [1, 3, 5],
               [2, 5, 8]])
     */
    void sum(int from[hh][ww], int to[hh][ww])
    {
        for (int x = 0; x < ww; x++) {
            to[0][x] = from[0][x];
            for (int y = 1; y < hh; y++) to[y][x] = to[y-1][x] + from[y][x];
        }
        for (int y = 0; y < hh; y++) {
            int total = to[y][0];
            for (int x = 1; x < ww; x++) to[y][x] = total += to[y][x];
        }
    }

    /* Return total neighbors in the neighborhood that includes (xmin+1,
       ymin+1), (xmin+2, ymin+1), ... (xmax, ymin+1), (xmin+1, ymin+2),
       ... (xmax, ymax).  xmin and/or ymin will be negative if the
       neighborhood is intended to encompass the leftmost and/or topmost
       cells; xmax may be >=ww-1 and/or ymax may be >=hh-1 if it is intended
       to encompass the rightmost and/or bottommost cells.
     */
    static inline int rect(int sums[hh][ww], int xmin, int xmax, int ymin, int ymax)
    {
        if (xmax > ww-1) xmax = ww-1;
        if (ymax > ww-1) ymax = hh-1;
        int ul = xmin < 0 ? 0 : ymin < 0 ? 0 : sums[ymin][xmin];
        int ur = ymin < 0 ? 0 : sums[ymin][xmax];
        int ll = xmin < 0 ? 0 : sums[ymax][xmin];
        int lr = sums[ymax][xmax];
        return lr - ur - ll + ul;
    }

    /* Return total cells in the 3×3 neighborhood centered on (x, y). */
    static inline int neighborhood(int sums[hh][ww], int x, int y)
    {
        return rect(sums, x-2, x+1, y-2, y+1);
    }

    static inline int should_live(int cells[hh][ww], int sums[hh][ww], int x, int y)
    {
        int n = neighborhood(sums, x, y);
        return cells[y][x] ? 3 <= n && n <= 4 : n == 3;
    }

    void generation(int from[hh][ww], int to[hh][ww], int scratch[hh][ww])
    {
        sum(from, scratch);
        for (int y = 0; y < hh; y++) {
            for (int x = 0; x < ww; x++) {
                to[y][x] = should_live(from, scratch, x, y);
            }
        }
    }

    void print_board(int cells[hh][ww])
    {
        putchar(' ');
        for (int x = 0; x < ww; x++) putchar('a' + x);
        putchar('\n');

        for (int y = 0; y < hh; y++) {
            putchar('a' + y);
            for (int x = 0; x < ww; x++) putchar(cells[y][x] ? '*' : '-');
            putchar('\n');
        }
    }

    /* Returns 1 if we should do another generation, 0 to quit */
    int prompt(int cells[hh][ww])
    {
        for (;;) {
            print_board(cells);

            putchar('>');
            fflush(stdout);

            int c1 = getchar();
            if (c1 == 'q' || c1 == EOF) return 0;
            if (c1 == '\n') return 1;

            int c2 = getchar();
            int newline = getchar();
            if (c2 == EOF || newline == EOF) return 0;

            int *cell = &cells[c1-'a'][c2-'a'];
            *cell = !*cell;
        }
    }

    int main()
    {
        int which = 0;
        for (;;) {
            if (!prompt(board[which])) return 0;
            generation(board[which], board[!which], board[2]);
            which = !which;
        }
    }

### Urbit's Nock ###

Urbit is Mencius Moldbug's effort to establish an internet with a
feudal, authoritarian structure, which he believes to be the ideal
structure for a society.  The basic foundation of Urbit is a
deterministic, reproducible virtual machine called Nock, named after a
political propagandist Moldbug admires despite Nock's private contempt
for Jewish people.  Nock implements a combinator-graph-reduction
instruction set encoded as integers.  The rest of the Urbit
distributed computation system is built atop Nock.

Nock's basic instruction repertoire is too limited to be usably
efficient for many of the tasks required for a distributed-computing
system like Urbit; this is partly compensated using a mechanism called
"jets".  The Nock implementation recognizes certain pieces of Nock
code at runtime and, rather than evaluating them instruction by
instruction, instead invokes a "jet" --- a subroutine written in C
that is hoped to produce an equivalent result.  Perhaps the most
egregious example is an implementation of the Markdown document markup
language, where a C implementation of Markdown is shamelessly
substituted when a particular Nock implementation of Markdown is
encountered.

Jets offer an apparent escape from the tradeoff between simplicity of
specification and usable levels of efficiency.  And, in theory, they
provide an unambiguous behavior specification for the native code to
adhere to.  However, they aren't a viable option for Veskeno, both
because they means that a practically usable implementation requires
an enormous amount of code whose contents must be guessed at by the
implementor, and because in practice that code will be buggy in all
modern implementations, since we don't yet have sufficiently powerful
formal methods for people to use them routinely, so if Veskeno used
jets, no Veskeno results would be reproducible in practice.

Consequently Nock is less suitable than even Brainfuck as a basis for
Veskeno.

### Simplicity ###

Simplicity is Russell O'Connor's verifiable smart-contract language,
designed for Ethereum.  It is a very interesting project, but like
Nock, it relies on jets to reach usable efficiency.  It's capable of
expressing only finitary computations --- those that could in
principle be expressed by a finite table of input-to-output mappings,
although Simplicity is designed to be able to practically express
finitary computations whose tables, though finite, would be too large
to construct explicitly.  Simplicity programs are guaranteed to
terminate because, like Bitcoin Script, it lacks an iteration
construct, relying on code repetition to achieve finite iteration.

For these reasons, Simplicity is even less suitable as a basis for
Veskeno than Nock.

### Wasm ###

### Smalltalk-78 ###

### The LuaJIT "bytecode" format ###

Lua's register-based "bytecode" format --- really a wordcode --- is
famous for its efficiency.  Considering this program in C:

    fib(n) { return n < 2 ? 1 : fib(n-1) + fib(n-2); }
    main(int c, char **v) { printf("%d\n", fib(atoi(v[1]))); }

And its Lua equivalent:

    function fib(n) if n < 2 then return 1 else return fib(n-1)+fib(n-2) end end
    print(fib(tonumber(arg[1])))

Compiling with `gcc -O -fomit-frame-pointer fib.c -o fib` with GCC
4.8.4, on this Atom netbook, it takes 101-116 ms to compute 3524578
with `./fib 32` and 399-406 ms to compute 14930352 with `./fib 35`.
Under PUC Lua 5.2.3, `fib.lua 32` takes 2.809-2.839 s and `fib.lua 35`
takes 11.856-12.211 s, both with the same results.  Under LuaJIT
2.0.2, `fib.lua 32` takes 196-212 ms and `fib.lua 35` takes
1.132-1.133 s.

So we can say that, on this crude microbenchmark, PUC Lua is 29-31
times slower than C, while LuaJIT is 1.6-2.9 times slower than C.
Reputedly LuaJIT 2's "bytecode" interpreter, which Mike Pall wrote in
assembly, is faster than many high-level languages' compiled code;
unfortunately there does not seem to be an option to disable the JIT
compiler for easy microbenchmarking.

It's somewhat to be expected that the extra type checks Lua must do
will slow down the process, especially in software, especially on an
in-order processor like this Atom.  Perhaps that accounts for the
speed difference between XIS, the RISCy spike above (1/20 native), and
PUC Lua (1/30).

CPython is the usual contrast here.  In CPython 2.7.6, this program
takes 4.963-5.176 s to compute fib(32), 42-51 times slower than C:

    #!/usr/bin/python
    import sys
    fib = lambda n: 1 if n < 2 else fib(n-1) + fib(n-2)
    print(fib(int(sys.argv[1])))

LuaJIT uses its own slightly different "bytecode" format.  [As
explained in the LuaJIT Wiki], the LuaJIT bytecode, like the PUC Lua
bytecode, has a fixed 32-bit-wide format with 8-bit fields. The opcode
is the least significant 8 bits; the 2-operand instructions have a
16-bit field as the second operand, which is usually an index into a
constant table.  There are 16 comparison ops (which conditionally skip
the following instruction, which is always a JMP), 4 unary ops, 17
"binary" ops (one of which, string concatenation, is actually
variadic), 6 constant ops, 7 "upvalue" and function ops, 11 ops for
manipulating Lua tables (like the GSET, GGET, and TGETB operations
above), 8 calling and iteration ops (like CALL and CALLM above), 4
return ops (like RET1 and RET0), 12 loop and branch ops, and 9
function-header pseudo-ops, for a total of 94 ops.

`luajit -bl fib.lua` dumps the bytecode:

    -- BYTECODE -- fib.lua:2-2
    0001    KSHORT   1   2
    0002    ISGE     0   1
    0003    JMP      1 => 0007
    0004    KSHORT   1   1
    0005    RET1     1   2
    0006    JMP      1 => 0015
    0007 => GGET     1   0      ; "fib"
    0008    SUBVN    2   0   0  ; 1
    0009    CALL     1   2   2
    0010    GGET     2   0      ; "fib"
    0011    SUBVN    3   0   1  ; 2
    0012    CALL     2   2   2
    0013    ADDVV    1   1   2
    0014    RET1     1   2
    0015 => RET0     0   1

    -- BYTECODE -- fib.lua:0-4
    0001    FNEW     0   0      ; fib.lua:2
    0002    GSET     0   1      ; "fib"
    0003    GGET     0   2      ; "print"
    0004    GGET     1   1      ; "fib"
    0005    GGET     2   3      ; "tonumber"
    0006    GGET     3   4      ; "arg"
    0007    TGETB    3   3   1
    0008    CALL     2   0   2
    0009    CALLM    1   0   0
    0010    CALLM    0   1   0
    0011    RET0     0   1

Many of these ops are specialized versions of basic operations; there
are, for example, three SUB instructions, two of which are specialized
to the case where one of the operands is a constant.  Some of the
operations are duplicated to provide the JIT compiler a place to
record its success or failure at JIT-compiling the loop body.

There is no specialized version of the ">=" operation for comparing
against a constant, so the "< 2" test in `fib` is compiled to KSHORT
(load immediate) followed by ISGE; similarly, there is no specialized
version of the `return` operation, so `return 1` is compiled to KSHORT
followed by RET1.

As on the SPARC or in Smalltalk-80, each function evidently has its
own set of registers; the main-program code at the bottom of the
listing above begins by getting some variables fro the global
namespace in registers 0, 1, 2, and 3, and then after calling
`tonumber` (in register 2) and `fib` (in register 1) it expects to
still find `print` in register 0, even though within `fib` the
argument `n` is evidently in register 0.  Thus no bytecode need be
emitted to save and restore context upon function call or return.

The three-operand nature of LuaJIT's bytecode saves some operations,
and thus some opcode dispatches, compared to the two-operand XIS code
above, which has 19 instructions in the `fib` subroutine rather than
15.  Where XIS has

      a_rr(mov, 0, 1),           /* fib: r1 := r0 */
      a_k16(lit16, 2, 2),        /* r2 := 2 */
      a_rr(sub, 2, 1),           /* r1 -= r2 */
      a_jl(1, 13),               /* if r1 < 0, go forward 13 insns */

LuaJIT has

    0001    KSHORT   1   2
    0002    ISGE     0   1
    0003    JMP      1 => 0007

although perhaps this has as much to do with LuaJIT discarding the
subtraction result rather than storing it in a destination register.
A recursive call `fib(n-2)` in LuaJIT is three instructions, and would
be two if not for the possibility of something having rebound the name
`fib`:

        0010    GGET     2   0      ; "fib"
        0011    SUBVN    3   0   1  ; 2
        0012    CALL     2   2   2

while XIS requires six, due to explicit saving and restoring of
argument registers:

      a_rs(push, 0),             /* save return value from recursive call */
      a_k16(lit16, 3, 2),        /* r3 := 2 */
      a_rr(mov, 1, 0),           /* r0 := r1 */
      a_rr(sub, 3, 0),           /* r0 -= r3 */
      a_call(-14),               /* call fib */
      a_rd(pop, 1),              /* pop saved return value into r1 */

I don't know if there's a way to get such implicit save/restore into a
Veskeno-sized spec; maybe make some of the "registers" index off a
stack pointer in memory that increments or decrements by some constant
after a call, like a lobotomized SPARC?  Where would you store the
return address --- would it eat a general-purpose register?

> If I remember correctly, the SPARC has 64 general-purpose registers:
  8 for global variables, and 48 in a "register window", of which 8
  are shared with the caller, 8 are local, and 8 are shared with
  callees --- so the window shifts by 16 on every call and return.
  The idea is that a simple, slow implementation can store all of
  these windows in RAM; a slightly less simple one can use 48
  registers and save 16 to RAM on every call and restore them on every
  return; and a more sophisticated implementation can maintain a
  circular buffer that only "spills" to RAM when it gets full.  Thus
  the "S" for "Scalable" in "SPARC".

Part of CPython's slowness is because CPython's bytecode is
stack-based rather than register-based, commonly requiring about twice
as many opcode dispatches as Lua.  The above function is 18 CPython
bytecode ops, rather than LuaJIT's 15; its leaf path is 7 ops rather
than 5, and its non-leaf path is 16 ops rather than 11, so for this
microbenchmark the dispatch penalty of stack-machine code is smaller
than that typical factor of 2.

      3           0 LOAD_FAST                0 (n)
                  3 LOAD_CONST               1 (2)
                  6 COMPARE_OP               0 (<)
                  9 POP_JUMP_IF_FALSE       16
                 12 LOAD_CONST               2 (1)
                 15 RETURN_VALUE
            >>   16 LOAD_GLOBAL              0 (fib)
                 19 LOAD_FAST                0 (n)
                 22 LOAD_CONST               2 (1)
                 25 BINARY_SUBTRACT
                 26 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
                 29 LOAD_GLOBAL              0 (fib)
                 32 LOAD_FAST                0 (n)
                 35 LOAD_CONST               1 (2)
                 38 BINARY_SUBTRACT
                 39 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
                 42 BINARY_ADD
                 43 RETURN_VALUE

As one specific example, this three-op sequence corresponds to a single
LuaJIT op:

                 19 LOAD_FAST                0 (n)
                 22 LOAD_CONST               2 (1)
                 25 BINARY_SUBTRACT

    0008    SUBVN    2   0   0  ; 1

Both LuaJIT and CPython separate the comparison and the jump into two
separate instructions; in LuaJIT the comparison is effectively a
conditional-skip instruction as on HP calculators.  Conditional skip
is very easy to implement in software for a fixed instruction length,
but very easy to implement *incorrectly* otherwise.

To complete the comparisons, the i386 code emitted by GCC in the tests
above was as follows:

     804844d:       56                      push   %esi
     804844e:       53                      push   %ebx
     804844f:       83 ec 14                sub    $0x14,%esp    ; useless waste
     8048452:       8b 5c 24 20             mov    0x20(%esp),%ebx ; n
     8048456:       b8 01 00 00 00          mov    $0x1,%eax       ; return 1
     804845b:       83 fb 01                cmp    $0x1,%ebx       ; n <= 1?
     804845e:       7e 1a                   jle    804847a <fib+0x2d>
     8048460:       8d 43 ff                lea    -0x1(%ebx),%eax ; n-1
     8048463:       89 04 24                mov    %eax,(%esp)     ; pass arg
     8048466:       e8 e2 ff ff ff          call   804844d <fib>
     804846b:       89 c6                   mov    %eax,%esi       ; save result
     804846d:       83 eb 02                sub    $0x2,%ebx       ; n-2
     8048470:       89 1c 24                mov    %ebx,(%esp)     ; pass arg
     8048473:       e8 d5 ff ff ff          call   804844d <fib>
     8048478:       01 f0                   add    %esi,%eax       ; sum results
     804847a:       83 c4 14                add    $0x14,%esp
     804847d:       5b                      pop    %ebx
     804847e:       5e                      pop    %esi
     804847f:       c3                      ret

This is 11 operations in the leaf-call base case and 19 operations in
the non-leaf recursive case.  To avoid redundant saves and restores
around the recursive calls, it keeps its local variables (`n` and the
return value from the first recursive call) in callee-saved registers
%esi and %ebx; this reduces the code size but has no real effect on
performance.  (If it had used caller-saved registers, as I did in the
XIS code, the initial root call to `fib` would have avoided the cost
to save and restore them, but that is not significant.)

It suffers from the shitty i386 C iBCS calling convention where
everything goes on the stack.  Revising it to

    __attribute__((fastcall)) int fib(int n)
    {
        return n < 2 ? 1 : fib(n-1) + fib(n-2);
    }

yields about 17% shorter runtimes with `gcc -O -fomit-frame-pointer
fib.c -o fib`, of 334-336 ms with `./fib 35`, and the following
improved code, with only 17 instructions (12% less):

     804844d:       56                      push   %esi
     804844e:       53                      push   %ebx
     804844f:       83 ec 04                sub    $0x4,%esp    ; still useless
     8048452:       89 cb                   mov    %ecx,%ebx       ; n
     8048454:       b8 01 00 00 00          mov    $0x1,%eax       ; return 1
     8048459:       83 f9 01                cmp    $0x1,%ecx       ; n < 1?
     804845c:       7e 14                   jle    8048472 <fib+0x25>
     804845e:       8d 49 ff                lea    -0x1(%ecx),%ecx ; n-1, arg
     8048461:       e8 e7 ff ff ff          call   804844d <fib>
     8048466:       89 c6                   mov    %eax,%esi       ; save result
     8048468:       8d 4b fe                lea    -0x2(%ebx),%ecx ; n-2, arg
     804846b:       e8 dd ff ff ff          call   804844d <fib>
     8048470:       01 f0                   add    %esi,%eax       ; sum results
     8048472:       83 c4 04                add    $0x4,%esp
     8048475:       5b                      pop    %ebx
     8048476:       5e                      pop    %esi
     8048477:       c3                      ret

(Adding `static inline` induces GCC to inline it into itself five
levels deep, resulting in 242 instructions that include 32 recursive
calls, and more than doubling the execution speed, to 157 ms runtime
for `./fib 35`.)

This is getting pretty deep into optimization hacks; the justification
is just that it illuminates some of the tradeoffs between different
instruction-set choices.

[As explained in the LuaJIT Wiki]: http://wiki.luajit.org/Bytecode-2.0

### SWEET-16 ###

As I wrote in "bytecode interpreters for tiny computers" in 2008:

> Steve Wozniak's SWEET16 16-bit virtual machine, included as part of
  Integer BASIC, supposedly doubled the code density of the 6502. The
  virtual machine itself was 300 bytes of 6502 assembly, implementing
  these instructions; here "#" means "[0-F]".

>     0x1# SET: load immediate               0x2# LD: copy register to accumulator
>     0x3# ST: copy accumulator to register  0x4# LD: load byte indirect w/ increment
>     0x5# ST: store byte indirect w/incr    0x6# LDD: load two bytes ind w/incr
>     0x7# STD: store two bytes ind w/incr   0x8# POP: load byte indirect w/predecr
>     0x9# STP: store byte ind w/predecr     0xA# ADD: add register to accum
>     0xB# SUB: subtract register from acc   0xC# POPD: load 2 bytes ind w/predecr
>     0xD# CPR: compare register w/acc       0xE# INR: increment register
>     0xF# DCR: decrement register           0x00 RTN to 6502 mode
>     0x01 BR unconditional branch           0x02 BNC branch if no carry
>     0x03 BC branch if carry                0x04 BP branch if positive
>     0x05 BM branch if minus                0x06 BZ branch if zero
>     0x07 BNZ branch if nonzero             0x08 BM1 branch if -1
>     0x09 BNM1 branch if not -1             0x0A BK break (software interrupt)
>     0x0B RS return from sub (R12 is SP)    0x0C BS branch to sub (R12 is SP)

> 0x01-0x09 and 0x0C have a second byte which is a signed 8-bit
  displacement. If you want a 16-bit jump, you can push it on the
  stack and RS.

> That's it, 28 instructions, 300 bytes of machine code to implement
  them. And I thought the 6502 was already reasonable on code density,
  so this was apparently quite a win.

It's notable to me that his only ALU operations here are ADD, SUB,
CPR, INR, and DCR; there are no bitwise operations, not even a
shift-right.  I'm guessing that SET was followed by a 16-bit immediate
to load into R#, though that isn't mentioned in my notes.

This is about the right level of complexity for Veskeno, although I'd
go 32-bit and trade some of the condition codes and branching options
for some bitwise operations.

Darius Bacon suggested that one of the reasons XIS was so slow was
that it didn't have a distinguished accumulator, so every binary
operation had to index an array three times: once to read each input
and once to write the output.  (It also had to extract the relevant
fields from the instruction word.)  As with stack machines, a
single-accumulator machine like the SWEET-16 reduces the number of
operands that need to be decoded and indexed, at the expense of
requiring a larger number of opcodes to be decoded for a given task.

### Chip-8 ###

Thanks
------

Discussions with Darius Bacon, John Cowan, and Sean B. Palmer greatly
contributed to the Veskeno design, although undoubtedly any of them
would be horrified at its deficiencies.