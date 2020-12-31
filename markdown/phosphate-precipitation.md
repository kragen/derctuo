I finally started trying out the recipe I'd come up with in Dercuano
for a kind of instant 3-D printing cement based on the precipitation
of water-soluble phosphate by pretty much any polyvalent cation.
(See "Likely-feasible non-flux-deposition powder-bed 3-D printing
processes".)

Initial experiences
-------------------

So I bought 2 kg of calcium chloride desiccant (AR$778, US$5.72,
US$2.86/kg) and 2 kg of diammonium phosphate fertilizer (AR$850, but
AR$470 of that was delivery; the marginal cost of the fertilizer is
AR$190/kg, US$1.40/kg).

The first observation is that this fertilizer is not pure diammonium
phosphate.  The individual prills have substantial variation in color,
and they do not dissolve fully in water, even at boiling.  A slight
ammonia smell evolves on boiling the water, and is absent from the
bags of fertilizer.  Filtering the liquid through a coffee filter
produces a transparent brown syrupy liquid, leaving most or all of the
solids behind (I'm doing this in cut-up aluminum cans, which are not
as good as glassware for seeing small amounts of cloudiness).

This phosphate liquid fails to dry even upon being sealed in a
room-temperature drying chamber sharing air with the calcium chloride
for several days.  (The chamber is Saran Wrap over the top of a
cut-off can, so it may be leaky, but I don't see any deliquescence on
the calcium chloride, so it's at least not very leaky.)

The phosphate liquid instantly produces a thick white suspension of a
fine precipitate upon being poured into a solution of the calcium
chloride.  Presumably this is some kind of calcium phosphate, along
with whatever fluoride may have been present as a contaminant.

After filtering through another coffee filter, it has the mouthfeel of
pure clay, making my teeth slide against one another with quite a bit
of difficulty, but no grittiness, demonstrating that there are no
crystals above the micron scale.  The taste is also slightly bitter
and salty, so I probably didn't wash the filtrate enough.  To the
touch of the hand, the suspension resembles a thin kaolin slip.  It
dries on the skin to a powder resembling rock-climbing chalk.

The calcium chloride seems relatively pure: it is plain white,
dissolves completely in water, and its only smell is a faint whiff of
quicklime.  Left in open air for a few days with a drop or two of
water, it gradually begins to deliquesce, producing a liquid that
feels "oily" because it's not evaporating.  Nevertheless, it is not a
food-grade chemical either, and it's labeled "for industrial use
only", so I shouldn't have tasted it.

I also added some of the phosphate solution to an aqueous solution of
some magnesium chloride I had lying around, which also produced an
immediate precipitate of, presumably, trimagnesium phosphate,
dimagnesium phosphate, magnesium ammonium phosphate, or a mixture.
This precipitate was slightly brown in color and settled out fairly
quickly, while the calcium precipitate did not visibly settle out at
all over the minutes before I filtered it.  Presumably both of these
differences owe to the crystals being larger or rounder than the
calcium precipitate.  The magnesium precipitate tastes the same as the
calcium precipitate.

Upon drying, the calcium precipitate has a consistency somewhat like
dried mud; I can pick up pieces of it with my fingers and break them
apart with my finger relatively easily.  However, rubbing it between
two fingers breaks it into a white powder too fine to have any gritty
feel, rather like cornstarch.  So it seems that the ammonium chloride
(or whatever) that is binding together the crystals of apatite (or
whatever) isn't able to hold them in clumps of more than a micron or
so in size; it might in fact just be van der Waals forces between the
apatite crystals.

After a couple of days of room-temperature air-drying, in the calcium
precipitate one crystal large enough to glint in sunlight can be seen
from the proper angle, but the rest of the powder still appears as a
matte-white, purely Lambertian surface.  Some 10% contraction on
drying is evident.

I have not managed to acquire clay yet, but it occurs to me that this
calcium phosphate powder (if that is what it is) is probably an
adequate alternative and may be a superior one.  The grain size is
about the same as that of clay, the expansion upon absorbing water is
probably smaller than clay's and perhaps insignificant, the crystal
habit can be made to be needlelike or (like clay) platy, the price is
only a little higher, and the aspect ratio of the grains should be
only a little worse.  Where it might be superior is that the apatite
cement I propose to selectively deposit can clearly bond well to these
grains, while its ability to bond to grains of clay remains a
significant unknown.  Also, the lower expansivity might enable it to
produce a higher-density final composite material.

### Gargouri et al.'s purification ###

A 2011 paper from Gargouri et al., "Synthesis and Physicochemical
Characterization of Pure Diammonium Phosphate from Industrial
Fertilizer", explains that in Tunisia the "diammonium phosphate"
industrial fertilizer is only 75% diammonium phosphate, the remainder
including "Co, Cu, Fe, Mn, Mo, Ni, Zn, F, As, Al, Hg, Pb and Cd".
They report getting their cheap industrial DAP almost as pure as the
laboratory DAP they bought from Fisher, simply by recrystallizing it
with 70% water and 30% alcohol, decoloring with charcoal.  They
report these results:

    | ppm                                      |   Fe |   Al |   Mg | Ag | As |   Co | Pb | Hg |  Si | Sn  | Ti |  Cr |   Zn | Cd | Cu | Ni | Mn |    V |
    | plant DAP                                | 6769 | 4273 | 4907 | 6  | 26 | 5419 | 22 | 3  | 150 | 382 | 93 | 525 | 1203 | 34 | 59 | 25 | 65 | 1341 |
    | plant DAP recrystallized (water-alcohol) |   24 |   37 |   14 | -  |  3 |    3 |  7 | -  |  70 | -   |  2 |  27 |   41 |  3 |  4 | 17 |  1 |   47 |
    | commercial DAP (Fisher)                  |   15 |   22 |    9 | -  |  3 |    2 |  7 | -  |  38 | -   |  - |  25 |   11 |  3 |  2 | 17 |  - |    9 |

This amounts to a reduction from 2.5% of these impurities down to
0.3%.  2.5% is a lot less than 25%, and I'm not sure what happened to
the other 22.5%; it might be impurities they also removed but didn't
measure, such as O (for example in OH or SiO₂), Ca, and F.  Their
analysis of the P and N content before and after their purification
(46% and 17.7% before, 49% and 18% after) does not support the
possibility that 25% of the original material was made of
non-ammonium, non-phosphate components.  However, some of the "25% of
impurities by weight" they cite might have been compounds like
ammonium fluoride and magnesium phosphate.  Or maybe it was just a
typographical error where they were missing a decimal point.

I should see if filtering with charcoal reduces the brown color.
Also, especially if I can get vacuum filtration set up,
recrystallization as per the standard procedure would eliminate
impurities that are still soluble in the solution after cooling.  Greg
Sittler suggested a water-driven venturi as a vacuum pump.

Another approach is to [make the solution basic][0], which will
precipitate hydroxides of (among other things) iron, nickel, copper,
and cadmium, but not ammonium or phosphate:

> g. All hydroxides are insoluble except those of the alkali
> metals. ... Ammonium hydroxide does not exist.

The usual way to do this is with lye, but I don't have access to lye;
however, household ammonia solution should also work.  Also, sodium
carbonate or sodium bicarbonate, which I do have, would precipitate
the transition metals ("e. All carbonates, sulfites, and phosphates
are insoluble except those of sodium, potassium, and ammonium"), but
by the same token you would think those would be precipitated already
in a phosphate-rich environment.  (Iron(III) phosphate, ferric
orthophosphate, is slightly soluble in water, but probably not enough
to give the brown color.)  So maybe I should try it and see what
happens but not expect success.

[0]: http://www.wiredchemist.com/chemistry/instructional/laboratory-tutorials/qualitative-analysis

### Other notes on next steps ###

Previously I'd written that you'd want to get the ammonium chloride
out of the finished piece by leaching it out with water.  But
[ammonium chloride evidently dissociates and "sublimes" at 337.6°][1];
initially I thought the mix of corrosive gases it produced would be
something I wouldn't want around, but apparently the gas on cooling
re-neutralizes to ammonium chloride rather than going around corroding
solid objects it encounters, so that might actually be a reasonable
way to remove the side product.

I guess the immediate next step is to dissolve some calcium chloride
in water and soak a little sand, a little of the supposed calcium
phosphate powder, and a little of a mixture of both with it, then let
it dry.  Actually ideally I would do this with both calcium chloride
and what I suppose to be DAP in order to see what the resulting
substances are like, since I suspect that calcium chloride in between
the grains of filler will work better than the other way around
because it will favor needlelike nanocrystals.  But that might turn
out to be wrong.

A further thing to try might be to use different pH levels.  I have
household ammonia to alkalinize the mix pretty thoroughly, but like
the ancient alchemists, no strong acids.

[1]: https://en.wikipedia.org/wiki/Ammonium_chloride#Reactions

### Witch-burnings, thoughtcrime, and Inquisitions: how to avoid torch-wielding peasants ###

As always with scholarship, there is danger from the thoughtless
prejudice of the ignorant, which so often has turned into violence, as
in the cases of Giordano Bruno, Aaron Swartz, Alan Turing, the Maya
codices, and Qin Shi Huang's burying of the scholars.

Ammonium chloride is on the national list of "controlled chemical
substances" which unauthorized people are not allowed to have or make;
but, then again, so are everyday products like aqueous ammonia
solution, lye, acetic acid, ethanol, isopropanol, methyl ethyl ketone
(the solvent in dry-erase markers), quicklime, slaked lime
(whitewash), acetone, ethyl acetate (nail polish remover), red
phosphorus (as found on matchboxes), nitromethane, sodium carbonate,
sodium bicarbonate, and kerosene.  Phosphorus, hydrochloric and
sulfuric acids, toluene, dichloromethane, and acetone are even in
"list 1" along with actual drugs I won't mention here.  Ammonium
chloride is in "list 3" along with ethanol, isopropanol, sodium
sulfate, and kerosene.

The definition of "product" is something of 30% purity or better of
(the total of) substances from lists 1 or 2 P/V (which I suspect means
"per volume"), or 20% purity or better of hydrochloric acid or aqueous
ammonia; except that if it's impossible to separate the substances by
physical means, higher concentrations may be approved on a
case-by-case basis.  Perhaps this is the reason I can buy vinegar at
the grocery store, lye at the hardware store, and nail polish remover
at the pharmacy, even though they are all in list 2: they are dilute.

Notably absent are sulfates (of anything but sodium), sulfur trioxide,
sodium percarbonate, phosphoric acid, nitric acid, and nitrates
(though sodium *nitrite* is included in list 3).

So, I think as long as I stay away from acetone and hydrochloric and
sulfuric acids, I shouldn't run into any pitchfork-wielding peasants.
