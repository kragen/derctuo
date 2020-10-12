Thinking about Marx generators last night, I realized that their
traditional elements — two-electrode air spark gaps, capacitors,
resistors, and a high-voltage, low-current DC power supply — are
probably sufficient to implement universal sequential digital logic,
It may be limited to a few kilohertz, but [air-gap
flashbulbs][2] can produce microsecond-level discharges when cooled by a
quartz heatsink, so faster speeds might be achievable.

[2]: https://en.wikipedia.org/wiki/Air-gap_flash

A Marx generator is a simple sort of RC relaxation oscillator used as
a source of high-voltage pulses when efficiency is not important.  A
series of capacitors in series, separated by similar-sized spark gaps,
are charged through a resistive network connecting their anodes and
another connecting their cathodes, which, as long as little current
flows, respectively maintains the anodes at similar voltages and maintains the
cathodes at similar voltages, so the voltage across each spark gap is
nearly the negative of that across each condenser; once this voltage
rises to a high enough level, the air in the gap experiences avalanche
breakdown with a large current and effectively connects two capacitors
in series, immediately overwhelming the breakdown of the adjacent
spark gaps.  This very rapidly produces a chain reaction and a very
high voltage, which, as I understand it, then discharges on a
timescale primarily limited by the self-induction of the elements of
the system, commonly nanoseconds to microseconds.

(Ordinary electrostatic discharges, like from walking across the
carpet and touching a doorknob, have rise times in the range of a
nanosecond or so, so the rise time will not be the limiting factor in
performance; the recovery time will.)

Without the voltage gain, you can build such a relaxation oscillator
with a single RC timing circuit with a spark gap in parallel with the
capacitor.  [Paschen's curve][0] has a minimum at one atmosphere at
327 V and 7.5 μm, tailing off to linear growth at 3.4 MV/m, so, for
manual work, it might be expedient to work with some 4 kV and gaps of
1 mm.  You can easily adjust the period of this oscillator by changing
the RC time constant, although the arc ignites at somewhat imprecise
times due in large part to the irregular availability of free
so-called "seed electrons" at the cathode, provided by photoelectrons
or background ionizing light and other particles.

[0]: https://en.wikipedia.org/wiki/Paschen%27s_law

(Common neon-sign transformers provide 2–15 kV RMS at 18–30 mA,
according to Wikipedia, a much less frequently lethal current than the
typical 500 mA of a microwave oven transformer.)

In particular, it's straightforward to make a triad consisting of a
"primary oscillator" running at, say, 500 Hz; a "reference oscillator"
at half that frequency, say 250 Hz; and a "bit oscillator" also
running at 250 Hz.  (At 1 mm the arc should ignite around 3.4 kV; with
a 4 kV power supply, that should take about 1.9RC, and the remaining
voltage after the arc extinguishes should be small; so, to get 250 Hz,
a 4-ms period, we could use an 10MΩ resistor and a 210 pF capacitor, a
1MΩ resistor and a 2100 pF capacitor, and so on, although the
resistors will start to get rather hot at lower resistances.)

In isolation these three oscillators will tend to drift relative to
one another, but I think this can be remedied.  If we use another
capacitor to couple the voltage spike from the triggering of the
primary oscillator into the reference oscillator and the bit
oscillator, we can advance the timing of the reference oscillator and
the bit oscillator so that they run at exactly half the frequency of
the primary oscillator, if they were running slower.  The voltage
spike early in the charging process won't be enough by itself to fire
the spark gap, but when the capacitor voltage is already nearly high
enough to strike a spark, it will easily overwhelm the dielectric
strength of the air in the gap.

Now the phase relationship between the reference oscillator and the
bit oscillator is quantized at either 0° or 180°, so the phase of the
bit oscillator stores a single bit of data.

For reliable storage, it is essential that the free-running frequency
of the reference and bit oscillators be slower than or equal to that
of half the primary oscillator; otherwise, they may spontaneously fire
early, resulting in phase drift and eventually a bit error.  Various
expedients are available: the use of a slightly larger resistance or
capacitance, of course, but also a slightly larger spark gap that will
*never* fire without the excess stimulation provided by the primary
oscillator; or a resistive network that charges the capacitor up to
only a fraction of the power supply.

It is worth mentioning at this point that a cascade of two or three
low-pass RC filters before the spark gap, rather than a single one,
can provide a more desirable voltage waveform at the spark gap — one
that remains lower for a longer fraction of the cycle, thus widening
the voltage safety margin against early firing.

Now that we have a reliable way of storing a bit, we have the problem
of constructing digital processes that evolve in time rather than
merely remaining stable, by coupling two or more bit-storage devices.
And in particular we want to be sure we can achieve chaos or
instability, known in the world of digital logic as "fanout" or
"amplification".  The example of the Marx generator shows that this is
definitely achievable.

One easy way to achieve amplification, though stepping outside the
framework of the Marx-generator parts mentioned above, is step-up
transformers.

Air-gap flashes pass a lot of charge between the main electrodes at,
typically, 20 kV, to produce the bright flash, triggering this with a
quartz-insulated "ignition tube" electrode at a much higher voltage
like 70 kV, but a much smaller amount of charge due to a lower
capacitance.  The higher voltage provides initial ionization in the
gap, which triggers the discharge of the lower-voltage but
higher-energy arc.

This provides energy gain, but not voltage gain — a high voltage is
used to switch a lower voltage.  A direct way to attack this problem
is by using a pulse transformer to step up voltages, so that a spark
at a relatively low voltage can produce a lower-current pulse at a
much higher voltage, used to trigger other gaps.

But, as we have seen above, more indirect routes are also available.

For example, if a 3400V spark gap has been charged up to 3000V, then a
700V impulse will trigger it to conduct, discharging the 3000V down to
perhaps 50V or 150V.  This impulse can be coupled in via a small
capacitor, requiring a correspondingly small amount of charge and
energy.  This can be facilitated by a small resistance between the
spark gap and the 3000V-charged capacitor — in this way a 700V impulse
coupled in thru a coupling capacitor need not charge the 3000V
capacitor, only the capacitance of the spark gap itself, which will
typically be in the picofarads.  If we suppose the spark gap is 10pF
and the pulse has a rise time of 1μs, a few hundred kilohms would
suffice, a number not normally considered a "small resistance", but
it's one or two orders of magnitude smaller than the other resistances
discussed above.

Another more indirect route is used by the Marx generator itself:
rather than using a *small* coupling capacitor to trigger the
discharge of a larger storage capacitor, we can couple the triggering
pulse through the large storage capacitor itself, suddenly increasing
the voltage on its other end by a factor of up to 2.

A third possibility involves resistive networks.  By charging a small
capacitor (to ground) through a resistive network from two or more
other capacitors (to ground), its voltage can be brought rapidly to a
weighted average of their voltages, and if a spark gap is in parallel
with it, it will either discharge repeatedly through the spark gap, or
not, according to whether its terminal voltage is greater or less than
the spark gap's breakdown voltage.  The pulses thus generated can be
used to trigger other, higher-energy spark gaps (and their time of
occurrence can be synchronized with the primary oscillator by coupling
in a little of the primary oscillator to them), or the resulting
larger current draw on the "input" capacitors can be sensed.

A particularly interesting possibility here is the use of such a
threshold device as a phase detector.  Given reasonable waveforms on
two bit oscillators, their instantaneous sum will rise above some
threshold for a while each cycle if they are in phase, but for a high
enough threshold, not at all if they are out of phase.  This provides
us with the operation XNOR; combined with negation and access to the
reference oscillator, we can construct in some sense all boolean
functions.

This same sort of threshold device may be of particular interest as a
display pixel, glowing or not according to whether its voltage reaches
the threshold and thus provokes repeated discharges.  Low-pressure
plasmas, xenon, and mercury vapor coupled with phosphors may be useful
in boosting visible light output for a given power level.

A fourth possibility is to *interrupt* a continuing arc, ballasted for
example by a resistor or the self-inductance of the wires, with a
voltage pulse that temporarily robs the spark gap of the tens of volts
necessary to continue conduction.  This, however, seems much more
precarious and sensitive.

Encoding bits directly as voltage levels rather than oscillator phases
seems like it would be more challenging, if feasible at all, unless
you are spending the enormous amount of energy required to keep a
spark gap in a state of continuous arcing, or find a way to employ
glow or corona discharge like an old Dekatron, which I suspect would
cost significant speed.  So I suspect the phase-encoding approach,
although less simple, is probably more practical.

The above falls short of being a fully worked out design for digital
sequential logic using spark gaps, resistors, and capacitors, but I
think it amounts to a convincing argument that it's practically
doable, though only over a fairly narrow voltage range in the normal
atmosphere (400–4000V); it might be easier to debug something closer
to the middle of that range, like 1200V, than designs near the limits,
like the 4000V I was considering above.  Reducing the pressure or
substituting a friendlier gas like argon (127V at 10μm) might help.

Sources of deviation from designed behavior include:

- Resistors changing resistance as they heat up and as voltages
  change: this is particularly a problem for old carbon-composition
  resistors, although modern high-precision metal-film resistors are
  not completely immune.

- Spark gap size variation between devices: this may be particularly a
  problem at lower voltages and lower gap sizes.

- Spark gap wear: the spark gaps will increase in size and decrease in
  smoothness over time as the frequent electrical discharges vaporize
  parts of the electrodes; moreover, some materials may form
  insulating oxide layers, increasing the breakdown voltage
  significantly.  This can be minimized by reducing the operating
  frequency; by using higher radices rather than binary, with the
  reference oscillator running at ⅓, ¼, or less of the primary
  oscillator frequency; by using electrodes of graphite, copper, or
  tungsten-copper such as Elkonite;
  or possibly by using a dielectric coolant liquid
  rather than gas, or by running cooling water through the electrodes.

- Spark initiation delay: avalanche discharge is triggered by a seed
  electron, which breaks free at a random time sometime after the
  avalanche threshold is passed.  In neon-tube logic of the 1960s and
  1970s, this problem was often remedied by adding radioisotopes to
  the electrodes or by keeping them brightly illuminated with visible
  or ultraviolet light, while in vacuum tubes it was remedied by
  heating the cathode with a resistive filament and also coating it
  with a low-work-function material such as an alkali-metal oxide.
  Other possibilities include using small gaps so that field emission
  is more important; using sharp points, perhaps even carbon fibers as
  used in modern ozone generators, to provide corona discharge in
  advance of the avalanche discharge; using larger electrodes, perhaps
  with anodes full of holes to permit light to illuminate the cathode;
  and suffusing the whole machine with slightly ionized gas or
  ultraviolet light.  The use of graphite might worsen this
  problem because its higher (≈4.6 eV) work function reduces
  field emission and the emission of photoelectrons.

Another crucial question about such devices is to what degree they can
be miniaturized and sped up.  Near-kilohertz discharge rates should be
straightforwardly achievable, but neon-tube logic topped out around a
kilohertz due to the relaxation time required for the gas to deionize.

Intuitively I would expect [higher-ionization-energy][4] gases to
recover faster — this is why [air-gap flashes][2] use air (primarily
nitrogen) instead of the more efficient xenon, because it gives
submicrosecond recovery times, and N₂ (1503 kJ/mol = 15.58 electron
volts) is close to optimal here, though, e.g., hydrogen (1488 kJ/mol =
15.43 electron volts) is close.  SF₆ (≈15.8 eV) may also be worth
considering.  Perhaps also higher pressures accelerate the recovery
time, accounting for the difference between the millisecond recovery
time of an ordinary low-pressure xenon camera flash and the 10μs cited
for xenon in Wikipedia's air-gap flash article.  [Electric-discharge
machining][3] routinely uses hundreds of thousands of sparks per
second in, typically, deionized water, which is pumped through the
spark gap at a high flow rate; typically this uses several hundred
volts to initiate the spark and an average of tens of volts and
several amps during cutting.

[3]: https://en.wikipedia.org/wiki/Electrical_discharge_machining#Sinker_EDM
[4]: https://cccbdb.nist.gov/ie1.asp

Both higher pressures and higher ionization energies would tend to
promote miniaturization.

So, because both air-gap flashes and EDM routinely have submicrosecond
recovery times, I think submicrosecond recovery times are probably
feasible, putting this kind of logic in the performance range of 1960s
CD4000-type CMOS.

Nanosecond-scale recovery times would probably be more challenging and
would probably require mechanical removal of the ionized dielectric;
for example, if the spark gap is 10 μm wide, it the electrodes can
very reasonably be 10-μm-diameter rods with holes in their center for
coolant flushing.  For the coolant to travel the 5 μm required to
clear the still-ionized material from the interelectrode gap in, say,
100 ns, it needs to be traveling at 50 m/s, Mach 0.15, and slightly
faster passing through the hole, which is probably achievable;
waterjet cutting machines achieve many times that speed through holes
in that size range, and the use of a gas would decrease the viscosity
far below what a waterjet must withstand.  This amounts to a volume
flow rate of under 10 microliters per second, again, plainly within
the bounds of feasibility.  We can conclude that active dielectric
flushing is a practical way to increase spark-gap logic operational
frequencies well into the megahertz, though probably not to 100 MHz.

What about energy usage?  If each spark gap has a capacitance of 10 pF
and discharges at only 500 V, it contains 1.25 μJ in its electrical
field at discharge time, which will be almost entirely dissipated by
the spark; at 1 MHz discharge rates this amounts to 1.25 W.  We might
thus suspect that active coolant flushing of some sort is necessary to
prevent the electrodes from entirely vaporizing, quite aside from its
potential utility for accelerating recovery times.

However, the minimal capacitance of a spark gap of 10 μm diameter and
10 μm spacing can be approximated with the infinite-plate formula C =
εA/d, which gives some 70 attofarads in this case, five orders of
magnitude smaller, so in fact the spark-gap capacitance will not be
the limiting factor in such cases, even using a high-permittivity
dielectric like water.

This in turn suggests an electrical energy cost on the order of 10
picojoules per bit operation, comparable to modern CMOS, although of
course that doesn't account for the energy cost of pumping all that
dielectric through the gap.  Also, such low energy costs per operation
probably require much higher operational frequencies — for RC = τ =
2.1 ms as above, you'd need a 30-teraohm resistor, which would
normally be called an "insulator".  So 1 nJ is probably achievable but
10 pJ may not be.

Conductive-mesh spark-gap electrodes may be a more effective way to
deliver light, dielectric, and coolant to the spark gap, permitting as
they do the use of spark gaps with electrodes much larger than their
interelectrode spacing without diminishing the fluid flow.

Over timescales not too long compared to the relaxation (deionization)
time of a dielectric, it may be feasible to use a flowing fluid as a
delay-line memory.  An input spark gap in the center of a tube of, for
example, rapidly flowing atmospheric-pressure xenon, produces a series
of plasma blobs which are rapidly carried downwind, still glowing;
some 10 μs later, an output spark gap also in the center of this tube
detects their presence by virtue of the discharges they ignite at well
below its usual breakdown voltage.  A partial vacuum behind a hole in
one of the electrodes of the output spark gap sucks these blobs into
the interelectrode gap.  To minimize the time-domain degradation of
the memory waveform, this entire stream of memory plasma is kept well
away from the walls of the tube by the xenon flowing around it, which
ideally would be in inviscid flow so that even the curl of the flow
field remains close to zero.  Operated at 10 MHz such a tube could
store at least 50 Manchester-encoded bits; gases that relax more
slowly than atmospheric-pressure xenon could afford larger capacities
and less-demanding operational frequencies.

Somewhat surprisingly for a digital-logic device that can plainly be
constructed by hand from Victorian-era materials, the above
dimensional figures strongly suggest that microscopic realizations of
this family of devices might be not only feasible but even practical,
particularly with higher pressures and modern insulators like teflon
(as opposed to sealing-wax and gutta percha).  With adequate plumbing,
they might be capable of speed rivaling modern solid-state
electronics, or at least 1980s solid-state electronics.  Spark gaps of
under a micron should be effective at a megapascal or so of gas, or
perhaps at atmospheric pressure with liquid dielectrics.

*****

Another amusing application of such relaxation oscillators might be as
microphones: below the Paschen minimum, even a very small change in
the electrode spacing should produce a very large change in the
breakdown voltage of the spark gap and consequently both the frequency
and the breakdown voltage of a free-running RC oscillator.  (I'm not
sure if it also increases the jitter.)  Above the Paschen minimum, it
should produce a smaller, nearly linear, but still fairly reliable
change in these variables.

The oscillation period also depends, of course, on the resistance and
capacitance, and in many applications it may be more practical to
modulate the capacitance rather than the spark-gap size.  3 mm of
ordinary glass ought to provide about 30 kV of dielectric strength, or
fifty times that if fused silica instead; the 2 cm² of a finger touch,
coupled with the relative permittivity of around 5 for glasses, gives
a capacitance of about 3 pF, which may be a detectable touch.  Lower
voltages and thinner materials may be a more practical way to detect
human touch, or simply mechanical deformation of air-dielectric
capacitors through levers.

*****

DTIC document 633669 from 1991, "Hydrogen spark gap for high
repetition rates", reports 10-μs recovery times to 17% for a 1.4 MPa
2.5-mm "unblown" hydrogen spark gap and 100-μs to 42%, about an order
of magnitude faster than air; this is attributed to hydrogen's high
thermal diffusivity.  That is, by "undervolting" the gap to 17% of its
normal breakdown voltage (some 120kV), they can trigger discharges at
100-kHz rates, or 10 kHz at 42% of its normal breakdown voltage.
Recovery to 90% takes about 1 ms for hydrogen and 10 ms for air,
"dominated by the cooling time of the hot channel" rather than its
deionization.  It also points out that narrower gaps permit closer gas
contact to metal surfaces, thus cooling the gas more rapidly, as well
as lower inductance and resistance, and that the gas requires some
time to recover its density after being rarefied by thermal expansion.
They were working with three-electrode trigatron-type devices and
report that "the recovery time varied little from millijoules to
kilojoules of transferred energy", though it would be unsurprising if
the picojoules I contemplate above did result in significant
variation.  (Hopefully the smaller energies would also result in
longer electrode life than the hundreds of shots at which they
reported substantial electrode wear.)

Above I haven't considered inductance, but of course at high enough
speeds at a given length scale, inductive impedance will dominate
resistance.  Decreasing the length scale also helps with this.

The related DTIC document A636361, "A laser-triggered mini-Marx for
low-jitter, high-voltage applications", describes an interesting way
of triggering spark gaps with ±700-ps 2σ jitter — by using ultraviolet
light to ionize SF₆ in the spark gap (in this case by a
frequency-quadrupled Q-switched Nd:YAG laser) it is possible to ignite
a plasma in a precharged spark gap, which then activates a Marx
generator with rise times in the range of 2 ns.  There are a variety
of ways that such spark gaps can detect light, ranging from the
reduced jitter from photoelectric seed electrons to this sort of
ionization-induced ignition-voltage reduction, and of course a
traditional Geiger counter is nothing more than a spark gap arranged
to detect ionizing light and other particles.

*****

A low-voltage way to try out some of these ideas is to replace the
spark gaps with transistors, or perhaps diodes, in reverse avalanche
mode, as in [Look Mum No Computer's Super Simple
Oscillator](https://www.lookmumnocomputer.com/simplest-oscillator),
which uses two unspecified terminals of a 2N3904 in parallel with a
10μF capacitor.  [Another, better-explained variant of the design uses
a
2N4401](https://www.learningaboutelectronics.com/Articles/Relaxation-oscillator-circuit-with-a-transistor.php)
with the emitter toward Vcc and the collector toward ground, in series
with an LED and in parallel with a 3300μF (!) capacitor.  (Thank you
very much to Hideki and splud on ##electronics for linking me!)

Folklore says red LEDs suffer reverse avalanche discharge around 5V
and often survive it, so they might be an alternative to the
transistor or spark gap.  Their lifetime might be limited in this
application, or it might not.  Probably something like a 1N4001, or
any ordinary rectifier or small-signal diode, would have an
inconveniently high breakdown voltage, which increases the chance of
damage to the diode, as well as power consumption and electric shock
risk.

In either case you're depending on properties of the components that
are not specified by the manufacturers because they're irrelevant to
their usual uses, so consistent results from component to component
may be hard to obtain.
