Sucrose’s enthalpy of formation is -2221.2 kJ/mol according to NIST,
and it contains 11 oxygens, 12 carbons, and 22 hydrogens.  What would
happen if you decomposed it in a very oxygen-hungry environment?  When
would the reduction of the sucrose be exothermic?

Candidate reduction products
----------------------------

If you were to strip off just the oxygens you would be left with
C₁₂H₂₂, which is four hydrogens short of being the saturated
hydrocarbon dodecane, whose standard enthalpy of formation is about
-350 kJ/mol (and of combustion about -7900, of which about 400 are
water condensing).  22 hydrogens are enough to fully saturate 10
carbons, producing decane, C₁₀H₂₂, with a standard enthalpy of
formation of -300 kJ/mol.

Bicyclohexyl is fully saturated as well and is C₁₂H₂₂, -273 kJ/mol
enthalpy of formation (and, of combustion, -7600).  1-dodecylene, with
a single unsaturated bond, is C₁₂H₂₄, thus needing a couple of extra
hydrogens, with -165 kJ/mol enthalpy of formation, thus being
considerably less stable.  1-dodecyne is also a C₁₂H₂₂, but is more
exotic; though Sigma-Aldrich will sell it to you, giving data like its
boiling point (215°) and density; they don’t include thermodynamic
data, but I’d guess it’s even less stable.  1,9-decadiene exists
(anyway Sigma will sell it to you and there are papers about using it)
and both (e, Z)-2,4-dodecadiene (C₁₂H₂₂) and 2,4-dodecadiene (C₁₀H₁₈)
are found in NIH’s data; the former “has primarily been detected in
saliva” (!!) but no thermodynamic data is available.  No alkadienes
higher than 1,7-octadiene have Wikipedia pages.

Suppose each of the monosaccharides decomposed separately, though?  We
might end up with 2C₆H₁₁, which doesn’t seem to exist, or C₆H₁₀ +
C₆H₁₂.  Cyclohexene (-40 kJ/mol Δf liquid) or 1,5-hexadiene (+50 to
100 kJ/mol Δf) are C₆H₁₀, while hexene (most common isomer, 1-hexene,
-74 kJ/mol Δf) and cyclohexane (-156 kJ/mol Δf) are C₆H₁₂.  None of
these seem super appealing especially compared to decane.

Including CO₂
-------------

So suppose you were to generate 2CO₂ (-393.5 kJ/mol each), sucking up
four of your 11 oxygens, leaving 7 oxygens and C₁₀H₂₂, decane, at
-300 kJ/mol, for a total of -489.0 kJ per mole of sucrose.  I guess
you’d need 1734.2 kJ/mol to make that enthalpically favorable?  That’s
247.7 kJ per mole of oxygen *atoms*.  Water’s enthalpy of formation is
-285.83 ±0.04 kJ/mol, which would seem to suggest that you should be
able to burn hydrogen with sucrose as an oxygen source, barely; the
trouble with this is that it would imply that you could get energy by
taking the oxygen out of sucrose, leaving its carbons and hydrogen
behind, and pairing it with new hydrogen from the environment.  This
seems fishy to me.

Trying to understand bond energies
----------------------------------

The <https://en.wikipedia.org/wiki/Bond-dissociation_energy> of O-H
bonds is typically on the order of 440 kJ/mol (e.g., in methanol), but
497 kJ/mol in water and only 360–380 kJ/mol in phenol; C-H bonds are
typically around 420 kJ/mol (e.g., in ethane, or 470 in benzene); and
H-H bonds are 436 kJ/mol.  So in the micro-reaction H-C-O-H + H₂ →
H-C-H + H₂O, we are ripping apart an H-H bond and a C-O bond, and
forming a C-H bond and an O-H bond in the resulting water.  [WC
claims](http://www.wiredchemist.com/chemistry/data/bond_energies_lengths.html)
the C-H bond is 411 kJ/mol and the C-O bond is 358 kJ/mol, though
those numbers are suspiciously exact for numbers outside of
context — [WP](https://en.wikipedia.org/wiki/Carbon%E2%80%93oxygen_bond)
gives a 360–380 kJ/mol range for some examples of the latter, for
example, and 372–556 for the former; another source, though, gives the
C-O bond energy in glucose as precisely the 358 kJ/mol given by WC,
and the C-H energy as 414.  So roughly we should expect to get (414 +
497 - 436 - 358 = 117) kJ/mol of hydrogen.  This is almost three times
higher than the discrepancy in the previous paragraph (each mole of
hydrogen produces a mole of oxygen) but in the same direction.

(I think the discrepancy is easily explained: it comes from the extra
four hydrogens glomming onto the carbon backbone and releasing their
own 400 or so kJ/mol, releasing more oxygen to react with the extra
hydrogen.)

However, if this is correct, I think it *doesn’t* imply that the
dehydration of sucrose, for example by heating, should be
exothermic — there we are breaking the H-C bond and the C-O bond and
forming an H-O bond instead, for a total of (497 - 414 - 358 = -275)
kJ/mol.  But I think that actually when this is done with vitriol
instead of heating it *is* exothermic, and the vitriol is a mere
catalyst; I’m not sure if this is correct.  [Supposedly the
spontaneous dehydration should be
exothermic.](https://chemdemos.uoregon.edu/demos/Spontaneous-Dehydration-of-Sucrose)

Aha, Darius Bacon points out that I’m not accounting for the carbon
finding a lower-energy state afterwards, where those carbon valences
are connected to something else — which is amusing, since that’s what
the first few paragraphs of this very note are about.  So if that
carbon goes and bonds to one other carbon from some other molecule on
each side, we gain like 350–380 kJ/mol per carbon (times two bonds,
but divided by two carbons per bond) which puts us back in exothermic
territory by like 100 kJ/mol, close to the hydrogen.  (And indeed the
page linked above says it’s due to the formation of graphene that the
reaction is exothermic.)

(There’s also a double-bonded oxygen in sugars which I’m ignoring; in
monosaccharides its bond energy is like 800 kJ/mol, and in sucrose it
glues the two monosaccharides together with an ether bond.  Hydrogen
might not liberate it, though above some temperature that would be
entropically favorable.)

Hydrogen vs. other reducing agents
----------------------------------

Hydrogen wouldn’t need to generate CO₂ to comfortably liberate oxygen
from sugars the way I discussed earlier, because it can saturate the
carbons just fine on its own.  Other reducing agents seeking to
oxidize themselves from sucrose might need to, thus gaining only 7
oxygens from the sucrose instead of the 10 or 11 gained by hydrogen;
so their oxidation products would need to have a more negative
enthalpy of formation than water's -285 kJ/mol of oxygen atoms.

So for non-hydrogen reducing agents, we get 7 moles of O per mole of
sucrose (342.30 g/mol), which works out to 112 g of O per 342 g of
sucrose, 33% oxygen by weight.  We might get even more if the reducing
agent can reduce CO₂, but at a higher enthalpy cost.  If the reducing
agent can’t reduce at least H₂O, it probably won’t be able to reduce
sucrose either.

Sucrose caramelizes at 186°, so if you want to reduce sucrose rather
than water, you’d better do it before that temperature.  Other
polysaccharides such as cellulose or chitin may survive to higher
temperatures, and things that can reduce sucrose can probably reduce
them too.
