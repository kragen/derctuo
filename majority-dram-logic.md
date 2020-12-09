Horowitz & Hill explain that modern DRAM column sense amplifiers
consist of a cycle of two CMOS inverters (two CMOS inverters in
antiparallel) with a pass transistor across them.  As long as the pass
transistor is passing voltage, the inverters are held in their
metastable state, with their input connected to their output,
producing shoot-through current the way CMOS does.  But when the pass
transistor is opened, the inverters become a latch precariously
balanced in its unstable equilibrium state, and the tiniest bit of
charge dropped on one node or the other can determine which direction
the latch falls.

This is done by connecting one of the bits of memory on the column to
the column sense line, and the other to a reference threshold voltage.
The capacitor pulls the sense line either up or down by the tiniest
bit, and that sets off a positive feedback slippery slope in the
newborn metastable flip-flop, which pulls the column sense line all
the way down to 0 or all the way up to 1.

This has the salutary effect of fully charging or discharging the
capacitor, thus refreshing the bit of memory; so the commonly-repeated
line that DRAM has destructive read, like core, is only sort of
true — the refreshing action is inherent to the way the sense
amplifier works.

It occurred to me that this is a potentially interesting design for
McCulloch–Pitts sequential logic, for SRAM, and for op-amp design.
Probably there's some flaw in these ideas that explain why they
haven't been pursued previously.

McCulloch–Pitts sequential logic
--------------------------------

The LGP-30 manual includes the full logic design for the machine; each
of its flip-flops (what we would today call latches) has a Boolean
function that tells it when to transition to 1 and another Boolean
function that tells it when to transition to 0, which are written out
in the manual so you can fix it when it breaks.  When neither of these
functions is true, it retains the same state it had previously,
because that is the natural state of a latch.  These functions were
computed monotonically with diode logic from signals already
available; the flip-flops provided not only all the sequentiality
needed, but also all the inversion and all the amplification.

In an analogous fashion, you could run three lines to a "neuron" made
thus of two inverters and a pass transistor: two input-output lines
and a "clock" (in quotes because it's level-triggered, not
edge-triggered) to control the pass transistor, which holds the
neuron's input-output lines at a metastable and identical level.  When
the pass transistor is deactivated, whatever differential external
drive is present on these input-output lines will be amplified into a
Boolean 1 or 0.

In CMOS this neuron is 5 transistors, and the pass transistor is
smaller if it's N-channel, but can be P-channel instead to get an
active-low "clock".

This neuron has four states:

1. Railing high, in which the pass transistor is open, and it drives its
   positive I/O line high and its negative I/O line low.
2. Railing low, in which the pass transistor is open, and it drives
   its positive I/O line low and its negative I/O line high.
3. Erased, in which the pass transistor is closed, and it drives both
   its positive and negative I/O lines firmly to their equilibrium
   voltage.
4. Sensitive, in which the pass transistor is open, but the I/O lines
   are still at their equilibrium level; this state is metastable, and
   at this point it starts transitioning to either high or low,
   depending on the signed difference in the drive currents impinging
   upon them from the outside world.

A simple way to use it is to hook its input lines to a
weighted-summing node driven by outputs of some set of neurons
activated by an earlier clock phase; resistors are one way to
accomplish this.  For example, given four neurons A, B, C, and D,
which have eight input-output lines A, /A ("A inverted"), B, /B, C,
/C, D, and /D, you can connect A-10k-D ("pin A to 10-kΩ resistor
connected to pin D"), B-10k-D, C-10k-D, /A-10k-/D, /B-10k-/D, and
/C-10k-/D.  First you clock the A, B, and C neurons, driving them out
of their metastable state into railing, while leaving the D neuron in
its metastable state.  Now the D line, though held metastable by the D
neuron, is being driven by A, B, and C to the average of their values;
if 1 is 3.3 volts, then they're trying to drive D to either 0, 1.1,
2.2, or 3.3 volts.  The /D line is being driven by the inverted
signals to either 3.3, 2.2, 1.1, or 0 volts respectively.  So, when we
finally clock D and let it go to a stable state, it will compute the
majority rule of the other neurons.  At this point we are free to
drive the other neurons back to a metastable state with the clock
signal.

At the level of state transitions, this is closely analogous to
Merkle's buckling-spring logic, but is not adiabatic or reversible;
the resistors are constantly dissipating energy, and so are the
push-pull transistors in the metastable neurons.  It's similar to the
McCulloch–Pitts threshold-based neuron model Turing used to explain
the Pilot ACE's logic design, but with a clock.

If we then hook up D in a similar way to drive the inputs of one or
more other neurons, a resistive path exists directly from A, B, and C
to those other neurons, which complicates our reasoning somewhat.
Their drive is somewhat attenuated, because it has to go through two
resistors instead of one, but it's there.  However, if we suppose the
output impedance of the inverters is on the order of 10Ω while the
coupling resistors are on the order of 10kΩ, then A, B, and C together
can only pull a low D output up by about 10mV, or a high D output down
by only about 10mV, if the power-supply voltage Vcc is 3.3V.

However, it may be inconvenient to use such powerful inverters and
such weak coupling between neurons, because the coupling needs to be
strong enough to reliably overwhelm random variation in the metastable
feedback currents once the pass transistor is opened.  (Also, if
you're doing this on an IC, large-value resistors are massive space
hogs.)  We might want the inverters' output drive to only be a little
stronger than the coupling resistors.  In that case, here are some
ways to reduce the extra parasitic coupling described above:

1. If we drive A, B, and C back to a metastable state once D is
properly latched, their drive will just attenuate D's drive rather
than adding uncertainty to it, because they've been erased before we
clock the neurons that depend on D.  We just have to not clock them
back out of the metastable state before we're done using D's results.

2. Up to a point, we can use progressively more resistance in
successive stages; for example, stage-0 neurons can take their inputs
through 10Ω resistors, stage 1 through 100Ω resistors, stage 2 through
1kΩ resistors, and stage 4 through 10kΩ resistors.  Obviously this
cannot continue indefinitely.

3. Instead of driving *both* of D's input/output lines from A, B, and
C, we can drive just *one* of them, while maintaining the other at a
reference threshold voltage, as is done in DRAM sense amps.  For
example, instead of wiring A-10k-D, /A-10k-/D, we just wire A-10k-D
and connect /D only to neurons that are activated at a later stage,
/D-10k-E for example.  This way, D mediates all the communication
between earlier and later stages, and when it's active, that
communication is almost entirely blocked.  As D is making its
decision, the /D line is pulled toward the equilibrium metastable
voltage level by the neurons it will later drive, since they are still
being held in the metastable state.  This scheme necessarily gives us
a single layer of negation at each clock phase; if we want to combine
different levels of negation, we need to run wires across phases.

4. We can lengthen the bucket brigade.  Suppose we have a chain of
neurons V:W:X:Y:Z with each one coupled to the next on both rails as
described earlier, but with lower resistances: V-100R-W, /V-100R-/W,
etc.  So if V goes metastable and then transitions into some new
state, its influence on Z's input is fighting against not one neuron
storing its old state, but three: W, X, and Y.

This parasitic-influence concern is even bigger when we try to get
fanout, influencing all of D, E, and F from the output of A — D, E,
and F can influence *each other*.

Sometimes it's not necessary to reduce that influence, though.

An interesting to note about this form of logic is that its
input-output directionality is entirely determined by the order of
clocking.  The V:W:X:Y:Z chain described above can copy bits either to
the left or to the right, depending on the order of clocking.  As Greg
Sittler points out, with three-phase clocking, like a stepper motor or
an amplifying digital CCD bucket-brigade; we can store one shiftable
bit per three neurons.  A two-dimensional array wired in such a way
can shift bits north, south, east, or west, though at the cost of _9_
neurons per bit and 9 potential clock phases.

Although this is already enough for universal computation (with
elaborate preprogrammed clocking schemes), coupling between the
neurons need not be limited to goofy McCulloch–Pitts weighted sums.

Pass transistors between stages in a bucket brigade can give us
bidirectional shift registers with only two neurons and two pass
registers per bit, rather than three neurons and three resistors; we
just clock the pass transistors to prevent the bits from going in the
direction we don't want.  (And, for two-dimensional shifts, the
advantage is even larger, requiring 4 neurons and 8 pass transistors
per bit.)

Diodes between neurons, rather or in addition to than resistors or
pass transistors, permit an input coupling to be stronger in pullup
mode or pulldown mode rather than symmetric, which permits more
compact logic than if you were to build it without diodes.  For
example, given A->-B, C->-B, (D-<||E-<)-100R-B (D and E each have a
diode down to them from a point which is connected to B through a
100-ohm resistor), F-1kR-B, then when B is in its metastable
"receptive" state, it will be pulled up if either A or C is high; if
not, either D or E can pull it down; if none of these conditions hold,
F drives B.

Naïvely you might think that such diodes would determine the direction
of data flow, but of course they don't; this example configuration
allows B to strongly pull down A and C whenever B is low or
metastable, which may not be desirable.  You can fix this thing with
tricks like (A->||C->)-100R-B, (D-<||E-<)-200R-B, which still allows a
high on either A or C to override D and E.

This limited kind of diode logic is not capable of doing the full
universal-up-to-monotonicity logic that traditional diode logic can
do.

Another very interesting possible way to couple neurons together is
via small capacitors, but this is tricky because it seems to be
inherently pretty glitchy.  Consider, for example,
(A-1pF||B-1pF||C-1pF)-D, where A, B, C, and D are the positive I/O
lines of four neurons, and suppose C and D are initially held in the
equilibrium state, suppose 1.6V, while A is 3.3V, B is 0.  So the
capacitors on A and B have been charged up to 1.6V, but in different
directions, while the capacitor on C is discharged to 0V.  If we then
open D's pass transistor to make it sensitive, then it will register
whichever of the following events happens *first*:

1. A transitions to its equilibrium voltage, producing a 1.6V negative
   voltage spike.  This spike would have a time constant of 10 ps, if
   the 10Ω-output-impedance hypothesis from earlier holds, if the
   capacitor were getting discharged through A's output impedance.
   However, this rapidly drives D to 0, so in fact the voltage across
   the capacitor rapidly returns to 1.6V in the same direction as
   before.
2. B transitions to its equilibrium voltage, producing a 1.6V positive
   voltage spike that drives D to 3.3V; this is otherwise equivalent
   to the case for A.
3. C transitions to either 0 or 3.3V, producing a 1.6V positive *or
   negative* voltage spike which then immediately propagates to D.  As
   before, the coupling capacitor between C and D doesn't change its
   state of charge.

So a group of such capacitor-coupled neurons that has *just* been
sensitized (by opening their internal pass transistors) is like tinder
for the least little glitch, which can propagate along it like
wildfire in any direction, being inverted wherever the neighbor
connections are via the negative outputs.  It's like a set of dominos
that can be automatically re-erected.  The pulse's propagation is
slowed if there are coupling capacitors to the I/O lines of other
neurons that are *not* in a sensitive state; these capacitors must be
charged or discharged through the series output impedances of the
transitioning neurons and the non-transitioning neurons, so the time
constant would be about 20 ps under the same assumptions as before.

(This is suddenly far afield of McCulloch–Pitts neurons!)

Such a pulse can be originated by a "destructive read" like that
described above on A or B, where by reopening their pass transistors,
we get a pulse that tells us what the neuron's state used to be.

One reliability concern: in the case where a coupling capacitor must
be charged or discharged because it's driving the input of a gate in a
stable (0 or 1) state, then until that happens, a voltage spike is
produced in the insensitive neuron that goes beyond the power rails,
1.6V beyond in this case, so the circuitry needs to be designed to not
explode under these circumstances.

If the internal pass transistors in this capacitor-coupled system were
driven as before by stupid canned clock-phase signals, this system
would not be very interesting.  However, we can also drive the pass
transistors from neuron outputs, so a neuron transitioning to 0 or 1
can make one or more neurons become sensitive or insensitive.  It's
important in this case that the sensitivity state be determined both
when the controlling neuron's output is 0 or 1 and when the neuron is
at its equilibrium voltage, whether by altering the threshold voltage
of the pass transistor or by some other means.  Specifically, I think
normally you want the pass transistor to remain open when the
controlling neuron is at its "erased" or equilibrium voltage, so that
you can make a decision about whether to erase the controlled neuron
without actually erasing it in the process.

I think this ability to enable or disable spike propagation in neurons
from the state of other neurons is all you need for universal
computation, although it may be somewhat complicated by the fact that
erasing the neuron's previous state also produces a spike.

You could consider using additional coupling pass transistors in
series with the coupling capacitors: A-1pF-Nch-B, say, where Nch is
the drain and source of an N-channel MOSFET.  However, this capacitor
will act like a bit of DRAM: whenever the MOSFET is open, it has a
stored charge from the difference between A and B the last time the
MOSFET was closed, unless it has been a very long time — tens of
microseconds or more, depending on the temperature and physical
construction.  So, if the voltage difference between A and B isn't the
same as it was when it was last opened, closing the pass transistor
produces a voltage spike on both A and B.  If one or both of them are
in a sensitive state, this could cause them to transition!

That behavior might be useful, but when it isn't, we can be sure to
only close these inter-neuron pass transistors when both A and B are
in their "erased" state; that is, their internal pass transistors are
already closed.  (It would also be okay if they were in their high or
low state, but that is harder to guarantee.)

Note that this means that the capacitor is storing a sort of trit:
when we close the coupling pass transistor, it can produce either a
positive pulse, a negative pulse, or no pulse.  But the "no pulse"
case may not be reliable, since slightly different operating voltages
between adjacent neurons might make it a small pulse instead.

The possibility of using coupling pass transistors in this way
suggests another way to produce two-neuron-per-bit bidirectional shift
registers with these neurons.  To shift right, suppose that initially
the bits are in the left neuron of each two-neuron cell, the right
neuron is erased.  The coupling transistor between the neurons is
closed, copying the bit onto the capacitor, and then opened.  Now we
erase the left neuron, so both neurons are erased, but the bit is
preserved on the coupling capacitor.  Now we sensitize the right
neuron, then close the coupling transistor again, thus storing the bit
in the right neuron — but inverted!  To finish shifting the bit into
the next cell, we do the same sequence of steps using the right
neuron, the left neuron of the cell to the right, and the coupling
capacitor and transistor between the cells rather than within them.  A
precisely analogous set of steps permits shifting to the left instead.

There might be a way to do this with only one neuron and one coupling
capacitor per bit, but it isn't obvious to me what it is.

Diodes may still be useful in this capacitor-coupled world; junction
diodes chop a 1.6-volt spike down to only about 1.0 volts, but that's
still plenty to knock over the domino, and they may be able to prevent
the propagation of back-biased spikes.  However, it may be necessary
to maintain a DC bias on the diode, or to use a PIN diode, to lower
the back-biased diode's capacitance enough to really block back-biased
spikes.

I suspect weighting different connections in the capacitor-coupled
world with various sizes of capacitor is not going to be useful,
because once a neuron goes sensitive, its new state will be determined
by the relative *timing* of different pulses — first come, first
served!  It won't be determined by the relative *sizes* of different
pulses unless they arrive in *very* close proximity.  So you might
still be able to do a sort of priority decoding by varying *delays*
rather than *impedances*, and you might be able to provide delays by
bypassing some neurons' I/O lines with additional capacitors to ground
(or, equivalently, Vcc), thus slowing their transitions.

The power use of these neurons, if built with MOS, is negligible in
the High and Low states, but heinous in the Erased and Sensitive
states, because we're deliberately provoking shoot-through.  However,
in the resistor-coupled McCulloch–Pitts version described first, we
*also* have constant power use whenever a High I/O line is connected
to a Low I/O line, even indirectly (perhaps they are two inputs to a
neuron used as a gate).  The diode-coupled version has less of this,
and the capacitor-coupled version eliminates it entirely, dissipating
energy only in Erased and Sensitive states and transitions, just like
regular CMOS.

If resistance is interposed in one or both of the positive feedback
paths within the neuron, it becomes possible to provoke state
transitions without an intermediate Erased state, simply by
overpowering the weak feedback drive with a lower-impedance external
drive.  This potentially permits the use of the technique described
earlier for the LGP-30, with separate Set and Reset logical formulas,
which might be coupled to the weakly-feedback-driven node using
diodes, so they can only pull it up or down strongly.  Rather than
interposing resistance, you could just use especially wimpy drive
transistors in the inverter, which has the additional flexibility of
permitting asymmetric drive capabilities — so, for example, a strong
low signal coming in on the I/O line can overpower the feedback and
reset the neuron, but a strong high signal cannot, as if that signal
were diode-coupled.

SRAM
----

Normal CMOS SRAM cells have 6 transistors or 8 transistors ("6T" and
"8T" respectively).  These neurons are 5 transistors.  I suspect that
adding a pass transistor to a data bus line gives us precisely the
standard 6T SRAM cell, but I need to check this out.

Op-amps
-------

So what happens if we take one of these neurons and try to use it as
an analog comparator?  We'd probably like to have separate input and
output signals; let's use a couple of pass transistors and an S&H
capacitor on each input to achieve this.  We wire
IN0-Nch1-(20pF-GND||Nch2-A) and correspondingly
IN1-Nch3-(20pF-GND||Nch4-/A) (that is, connect an N-channel MOSFET
Nch3 from IN1 to a node connected to ground through a 20-pF capacitor,
and to the I/O line /A via another N-channel MOSFET Nch4).  To sample,
we open the MOSFETs Nch2 and Nch4, then close Nch1 and Nch3 (with
make-before-break ordering, probably).  Then we wait for the dual 20pF
capacitors to ground to charge and discharge (they could probably be a
single 20pF capacitor between these nodes, really).  Once they're
adequately charged (failure to wait long enough will induce excessive
hysteresis) we open Nch1 and Nch3, erase the neuron, sensitize the
neuron, and simultaneously close Nch2 and Nch4.  The differential
voltage thus imposed on A and /A will kick it out of the Sensitive
state into either High or Low.

At this point we can return to sampling again, while the output state
continues to rail in A and /A until the clock says it's time to do
another comparison.

An interesting thing about this setup is that its input offset voltage
doesn't depend on transistor matching in the usual way, although a
mismatch in the equilibrium voltage between the two inverters will
provoke some offset — the initial erased voltage will be somewhere in
between the two inverters' equilibrium voltages, so one of them will
tend to start rising and the other falling.  But this offset can be
arbitrarily ensmallened by weakening the inverters' output drivers (as
before, possibly by putting an output rsistance on them) at the cost
of speed, or embiggening the S&H capacitors at the cost of bias
current.  The R<sub>on</sub> of the erasing pass transistor will also
provoke some imbalance during the erase state — if one inverter has a
stronger high-side output drive, it will tend to pull its end of the
erasing transistor up, and *mutatis mutandis* if it's the low-side
output drive that's stronger.  This R<sub>on</sub> also means that the
inverter with the lower input threshold will tend to have lower output
voltage, which further reduces the precision of the initial unstable
equilibrium.

The input bias current of this setup is spiky, coming whenever we take
a new sample, so we can reduce it arbitrarily low by taking samples
less often, a choice which also enables us to completely prevent
oscillations above half the sample frequency.  If our rails are ±15V,
then every time we sample, we briefly spike one input to -15V and the
other to +15V.  If the input impedance is 1kΩ, then these spikes would
decay with a time constant of 20ns.  If the inputs are equal (as is
usual for an op-amp), these spikes will total 30V and 0.6 nanocoulombs
(regardless of what the input impedance is); if we use a sampling
frequency of 1MHz, that's up to 0.6 mA of input bias current, which is
horrendous, and all of which is balanced and therefore also an input
offset current, which is worse.  But if we reduce the sampling
frequency to 1kHz, we reduce it to 0.6 μA, which is reasonable if not
excellent.

We could perhaps reduce the magnitude of these voltage spikes by only
drawing from the S&H capacitors until the neuron is safely out of
equilibrium, then opening Nch2 and Nch4; 500mV out of equilibrium is
probably plenty, and if there's a bit of resistance at Nch2 and Nch4,
we might be able to keep the offset voltage imposed on the capacitor
down to 50mV or so.  That is, once the horse is safely running free,
slam the barn door shut as fast as you can.  This would drop the input
bias current by three orders of magnitude, down to a reasonable 0.6 μA
or good 0.6 nA in the example, assuming the input transistors are
perfect.  (0.6 nA at 30 V would require them to have 50 GΩ of
impedance when turned off, which is unlikely.)  With a bit of
cleverness, you might be able to break the circuit at roughly the
point where the sampling caps have been "refreshed" by the amount of
charge they lost when they initially unbalanced the neuron.

It might make more sense to just use JFET followers to drive the S&H
inputs, but then of course we get back into the question of transistor
matching.

So far we only have a clocked differential-output analog comparator
whose output spikes down to zero on every clock cycle, a problem that
could be masked by a pass transistor, capacitor, and drain follower
glommed onto its output, carefully clocked to exclude the spikes.  To
transform it into an op-amp, we need to filter its output.  The
*ideal* way to do this might be with a SAW or coax delay-line filter
to notch out the clock frequency and all its harmonics, plus a good
number of subharmonics.  But it's probably more practical to eliminate
the spikes in the time domain by the method described above, then use
an RC filter to get some crude averaging.

(In some cases it might make more sense to feed the raw railing output
to a power amplifier, LC low-pass filter the output, and use *that*
for the negative feedback; that way you get a class-D amplifier
instead of an op-amp.)

I'm not sure what the impact will be of gate charge injection in the
S&H circuit, but it seems like it could be a potential problem.

The potential open-loop gain of this sort of amplifier is ridiculously
huge, because by reducing the clock rate the input power can be
reduced to almost arbitrarily low levels.  It seems plausible that it
could detect an input difference of 1 mV (indeed, if it can't, it's
not going to be very useful as an analog op-amp); if it's getting 1 nA
of offset current there, it's consuming a picowatt of input power.
But a small amplifier of this design could produce an output swing of
30 V at 500 mA, 15 watts, in response to that millivolt input swing.
That's 16 orders of magnitude more power, which in some sense
corresponds to 8 orders of magnitude more voltage, which is how
open-loop gain is normally measured.

Given how commonplace it is to use sample-and-hold circuits, analog
comparators, and D flip-flops, this is surely a family of op-amp
design that has been tried previously, even if the analog comparator
in question wasn't specifically based on the
antiparallel-shorted-inverters approach.
