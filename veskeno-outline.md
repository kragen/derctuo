An outline of the design process leading up to the Veskeno virtual machine
==========================================================================

The primary goal of Derctuo is to present some calculations and
computational simulations in a reproducible fashion, so that it is
possible for other people to build on them.  Unfortunately, and quite
surprisingly, no suitable medium for such things currently
exists — except in the limited sense that bytes and computers are
potentially such a medium.  But a raw sequence of bytes is meaningless
without some kind of interpretation, a “file format”, and as far as I
can tell, no suitable file format currently exists.

“Veskeno” is the name I have adopted for such a file format, which
unfortunately required the development of a new virtual machine for
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

The objective of Veskeno: make software, specifically Derctuo, run reproducibly
-------------------------------------------------------------------------------

Since at least Church, Turing, and Gödel, we know that we can, in
theory, come to the same kind of consensus about the behavior of an
*algorithm* — in theory any algorithm whatever can be executed on any
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
luck, more quickly.

The following priorities are suggested in order to achieve this:

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

This set of priorities leads to a very unusual set of design
tradeoffs, one so alien to modern mainstream virtual machine design
that the comment was heard, “I feel like this is designing a weapon or
something.”

A Galaxy A10 (30 million sold in 02019) has 2 GiB of RAM and eight
Cortex-A cores running at 1.35 to 1.6 GHz, capable in all of perhaps
20 billion 64-bit multiply-accumulate operations per second, plus
a Mali-G71 MP2 GPU, which I think is about 50 gigaflops on two cores.
A 1980s video-game might have 1 MiB of RAM and execute a million
16-bit multiply-accumulates per second.  So the performance overhead
budget here is about a factor of 2048 in space and a factor of some
131072 in time, though of course greater speed and less overhead would
be desirable, since it would make much more elaborate computations
reproducible.  Typical straightforward low-level virtual machines can
achieve time overhead factors of 3–20 and space overhead of 1.1–4, but
we don’t have to come close to that.

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
and probably counterproductive.

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

An untyped 32-bit register machine with mod-2³² wraparound
----------------------------------------------------------

The Veskeno virtual machine has 16 CPU registers and a RAM array;
programs using a stack store the stack in the RAM.  To ease compiling
existing C code for Veskeno, the registers are 32 bits, despite the
hassles that entails in languages like Java or on 16-bit hardware; it
poses no difficulty for Veskeno implementations in languages like C or
Scheme.

The only arithmetic operations it offers are addition and subtraction,
which behave mod 2³² as you would expect.

### The fibterp spike ###

As a simple experiment to get a handle on software complexity and
interpretive slowdown, I hacked together the following minimal
simulator for such a machine, together with a dumb Fibonacci program
for it; this took 96 minutes and 119 lines of C, 21 of which are the
dumb Fibonacci program in a sort of assembly language.  This virtual
machine has 11 instructions and word-addressed memory, but I think
Veskeno itself will have more like 16 instructions and byte-addressed
memory.

    /* Simple little RISCy bytecode interpreter as a sort of quick spike
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
estimated](vector-vm.md) at 32×.  Above I estimated that a Samsung
Galaxy A10, for example, can do about 70 billion multiply-accumulate
operations per second, but single-threaded unvectorized code on it
won't get more than about 1.6 billion, 44 times slower; out-of-order
processors with more execution units close the gap a little.  It would
not be surprising for a virtual machine that exploits such data
parallelism to exceed the speed of optimized single-threaded
unvectorized C.

Multiplication and division?
----------------------------

I’m not yet sure whether Veskeno should have a multiplication
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
turbulence inside the disk drive, they can produce XXX

timers

games

keystrokes

Related work
------------

### Preservation through emulation, e.g., SIMH ###

### Lorie’s UVM ###

### Chifir ###

### The JVM ###

### ActivePapers ###

### Nix and Guix ###

### The Cult of the Bound Variable ###

32-bit unsigned

### Corewar Redcode ###

### Wirth RISC ###

### Brainfuck ###

### Urbit ###

### Simplicity ###

### Wasm ###

### Smalltalk-78 ###

Thanks
------

Discussions with Darius Bacon, John Cowan, and Sean B. Palmer greatly
contributed to the Veskeno design, although undoubtedly any of them
would be horrified at its deficiencies.