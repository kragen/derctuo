Today the study [“Vitamin D Status in Hospitalized Patients With
SARS-CoV-2 Infection”][0] was published.  The key point from the
abstract is

>  Vitamin D deficiency was found in 82.2% of COVID-19 cases and 47.2%
>  of population-based controls (p<0.0001)… No causal relationship was
>  found between vitamin D deficiency and COVID-19 severity as a
>  combined endpoint or as its separate components.

[0]: https://academic.oup.com/jcem/advance-article/doi/10.1210/clinem/dgaa733/5934827

This strongly suggests a causal role for vitamin-D deficiency in covid
hospitalization, and probably in risk of infection.  This might
explain some of the curious patterns, where the previously healthiest
countries are the ones where COVID-19 has spread fastest.

[There are several other such studies][9] and a [website that
summarizes them][20].

[9]: https://en.wikipedia.org/wiki/Management_of_COVID-19#Vitamin_D
[20]: https://vitamin-d-covid.shotwell.ca/

Bayesian calculation from this study suggests a risk ratio of 4.2
-----------------------------------------------------------------

Let’s take a look at the basis for this statistic:

> including 216 patients aged ≥ 18 years, with confirmed COVID-19
> admitted to the University Hospital Marqués de Valdecilla in
> Santander, Northern Spain from March 10 to March 31, 2020, and 197
> sex-matched population-based controls recruited from the Camargo
> Cohort (14,15) during their last follow-up visit on January–March of
> the past year.

I’m a bit fuzzy on all this statistics stuff, so I’m going to walk
through this in baby steps to make sure I get it right.

During that period [Spain went from 3'258 confirmed covid cases to
111'541][1], out of a [population of 47'400'000][2].  This range of
34× during the study period makes it a bit difficult to do the
calculations I want to do, but let’s use, say, 50'000, since
presumably the vast majority of the patients admitted to hospitals
during that period were admitted toward the end.  (And as we’ll see at
the end, this doesn’t matter anyway.)

[1]: https://en.wikipedia.org/wiki/COVID-19_pandemic_in_Spain
[2]: https://en.wikipedia.org/wiki/Spain

Presumably only a fraction of the people with COVID-19 were
hospitalized; at present 157'881 people have been hospitalized out of
1'046'132 PCR-confirmed cases, or 15%.  So maybe 7500 people were
hospitalized with covid during that time, and maybe we can assume that
the 82.2% number is typical of them: 6200 hospitalized covid patients
with vitamin-D deficiency, 1300 hospitalized covid patients without
it.  Out of the total population, if we assume the 47.2% number from
the previous year is typical, we have 22 million people who weren't
hospitalized with covid and were vitamin-D deficient, and 25 million
people who weren't hospitalized with covid and weren’t vitamin-D
deficient either.

So, 6200 out of 25 million vitamin-D-deficient people were
hospitalized with covid at the time (250 out of every million people),
and 1300 out of 22 million non-vitamin-D-deficient people were (59 per
million).  So the relative risk is 250 ÷ 59.

**That’s a relative risk of 4.2.** If you were vitamin-D deficient in
Spain at that point, you were 4.2 times as likely to get hospitalized
with covid than if you weren’t deficient, probably because the
deficiency raises your risk of catching covid.  A lot.  This is a
*huge* relative risk.  ([Or is it?][3] [Apparently risk ratios are the
thing to use instead of odds ratios.][4])

[3]: https://slatestarcodex.com/2020/04/07/never-tell-me-the-odds-ratio/
[4]: http://itre.cis.upenn.edu/~myl/languagelog/archives/004767.html

Note that 6200 and 1300 are products of my estimate of the number of
people hospitalized with 82.2% and (100% - 82.2%) respectively.  So
you get the same relative risk regardless of whether the actual number
of hospitalized people was 750, 7500 or 75'000.  (At 750'000 or more
it might start to matter if people who later got covid were excluded
from the “population-based control group” or not, but even now, in
October, Spain hasn’t hospitalized nearly that many covid patients.)

Possible confounding factors exist
----------------------------------

Are there other explanations, other than vitamin D deficiency causing
an increased risk of serious covid, probably through causing an
increased risk of covid?

Well, the most obvious is that covid could *cause* vitamin D
deficiency, for example by interfering with digestion or by directly
depleting vitamin D stores.  I don’t know enough about vitamin D
metabolism to be very confident in this, but I don’t think it’s very
likely; as a fat-soluble vitamin, it can be stored for long periods of
time, so I think the body usually contains a fairly huge amount
compared to what it can use in the first week or two of a covid
infection.

A second possible connection is for a common cause to produce both
vitamin D deficiency and covid susceptibility.  As Aaron Ferrucci
points out, this could be something as simple as spending time indoors
and not getting exercise outside.

The above is not exhaustive, but it hopefully clarifies that the
posited protective effect of vitamin D against covid might not really
exist, despite the astounding risk ratio computed above.

There are other recent papers like [“Vitamin D and COVID-19”,
Bilezikian et al.,][8] that strongly suggest a causal mechanism,
though that one cautions that it’s “a putative clinical link that at
this time must still be considered hypothetical.”

[8]: https://pubmed.ncbi.nlm.nih.gov/32755992/

Doses and sources: 2000 IU daily from mostly supplements
--------------------------------------------------------

Normally 40 IU is 1 μg.  I’m not sure if the weird IU-density
variability thing that comes into play with some other vitamins is at
play here, but for now I’ll assume it’s not.

The [US RDA][6] is 600 IU or 15 μg, with the tolerable upper intake
level being 4000 IU or 100 μg; Australia and NZ instead recommend
10–80 μg/day, and the EU 15–100 μg/day, same as the US.

[6]: https://en.wikipedia.org/wiki/Vitamin_D#Recommended_levels

Given this, I’d think supplementing with a dose of some 2000 IU/day
would be strongly advisable, as well as getting lots of UV-B exposure.

[Gwern suggests][12] it’s important to take it [in the morning, not at
night][18], and reports that he’s taking 5000 IU per day.  He says
overdose starts around 70'000 IU, so it might be a good idea to start
the vitamin-D regimen with a single dose on the order of 20'000 IU.
He also suggests that “an hour on the beach” is likely to give you
10'000 IU, and so this should be a safe daily dose.  The [Endocrine
Society Clinical Practice Guideline on the subject][14] counts 4'000
IU daily as “maintenance tolerable upper limits”, and suggests that
adequate blood levels “may require at least 1500–2000 IU/d[ay]”.  It
confirms Gwern’s thing about sunlight: a minimal erythemal dose (mild
first-degree sunburn) is 20'000 IU!

[Gwern also recommends vitamin D supplementation for life
extension,][17] quite aside from covid and nootropic reasons: it
extends your life by an expected four months or so.

[ChristianKI wrote a vitamin D primer on Lesswrong][13], recommending
among other things to take vitamin K2 as well; this is a common
practice for OTC supplements.

[12]: https://www.gwern.net/Nootropics#vitamin-d
[13]: https://www.lesswrong.com/posts/c5aycbSsSc38XWPEc/taking-vitamin-d3-with-k2-in-the-morning
[14]: https://academic.oup.com/jcem/article/96/7/1911/2833671 "Holick et al., J Clin Endocrinol Metab, July 2011, 96(7):1911–1930"
[17]: https://www.gwern.net/Longevity#vitamin-d
[18]: https://www.gwern.net/zeo/Vitamin-D

The CPG also mentions that the circulating half-life of the 25(OH)D
form is 2–3 weeks, which reinforces my earlier-mentioned skepticism
that a covid infection could drop vitamin D levels rapidly enough to
provoke a deficiency in the study linked.  And it mentions that body
fat sequesters the vitamin, increasing the risk of deficiency, which
might explain several puzzling things about covid, including how
smokers are at lower risk for covid in countries with high
obesity — except that smoking *lowers* vitamin D [in Copenhagen][15]
and [in Guangzhou][16], so the smoking link is nonexistent.

[15]: https://pubmed.ncbi.nlm.nih.gov/10602348/
[16]: library/smoking-vitamin-d-e010946.full.pdf "https://bmjopen.bmj.com/content/6/6/e010946"

Damn, this CPG is a fucking goldmine.

[Getting such large quantities of vitamin D from food][19] is very
difficult.

[19]: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3941824/

Vitamin D₃, [cholecalciferol, is used as rat poison][21] and possum
poison with a LD₅₀ of about 10 mg/kg, so if I were a possum the
acutely lethal dose would be about 1.2 grams, about 48 million IU.  In
humans there are concerns with continued doses over 4000 IU per day,
as mentioned above.

[21]: https://en.wikipedia.org/wiki/Cholecalciferol#Rodenticide

### Fish: I’d need to eat 450 g of mackerel per day costing US$1.28 ###

This can of _jurel_ (“jack mackerel”, the marketing name for horse
mackerel, which is not a mackerel) says it contains 300 grams of fish
and 12.5 grams of fat.  This [supposedly contains 4.6 IU of vitamin D
per gram][5], so the can I just ate should have given me 1380 IU, two
days’ worth of the minimal allowance but only about 8 hours of the
upper limit.  I don’t remember how much it cost, but maybe AR$150,
88¢, 640 microdollars per IU, or US$1.28/day for 2000 IU/day.

[5]: https://en.wikipedia.org/wiki/Vitamin_D#Sources

### Eggs: useless ###

An egg only has about 44 IU, 1% of the upper intake level, 2% of my
goal, and 14% of the US RDA.  Eating four dozen eggs a day to get to
2000 IU is probably not a good idea.  I don’t think eggs contribute
enough to be worth consideration here.

### Cod liver oil: the best option at 15¢/day ###

Cod liver oil (_aceite de higado de bacalao_) as a supplement is 100
IU/g or 450 IU per spoonful, and eating several spoonfuls of it per
day seems plausible (and is the recommended dose).  [150 mℓ of cod
liver oil goes for AR$825][7] in a bottle, which is US$4.85,or
3.2¢/mℓ, which is basically a gram I guess.  This works out to 72
microdollars per IU, or 14.4¢ per day for 2000 IU/day.

[7]: https://articulo.mercadolibre.com.ar/MLA-855290800-aceite-de-higado-de-bacalao-150-ml-_JM

### Fortified milk: not useless but even worse than fish ###

This box of La Serenísima instant dry whole milk says it contains
400 g to make 3 ℓ, and a 200-mℓ serving contains 2.1 μg (84 IU, 42% of
the daily value, which I guess we can deduce is 5 μg, ⅓ of the US/EU
value and ½ of the .au/.nz value.)  This serving supposedly has 26⅔ g
of dry milk in it, so it’s about π IU of vitamin D per gram of dry
milk, a bit less than the fish.  I think the price per gram is also
similar or maybe a little higher.  I’d need to eat 600 g, a box and a
half of dried milk, per day, to reach 2000 IU per day.  Eating nearly
a kilo of dried milk per day, consisting mostly of lactose, seems even
less appealing than eating hundreds of grams of fish.

On the plus side, it’s a lot more feasible to eat 600 g of dried milk
than it would be to drink 4.8 liters of milk.

Perhaps not entirely coincidentally, this supplementation level is
precisely the maximum that would be allowed in the US.

The Armonía brand cut-rate instant dry whole milk is basically the
same.

### Pills: 3.7¢ per day, but perhaps less trustworthy ###

There are several brands of vitamin D supplements available;
[Puritan’s Pride][10] sells 100 softgels of supposedly 250 μg each
(10k IU) for AR$3120 (US$18.35).  This is 18.35 microdollars per IU or
3.7¢ per day.  [Now Foods][11] sells 120 softgels of the same dose for
AR$3850 (US$22.60), or 22.6 microdollars per IU or 4.5¢ per day.
Their recommended dosage is one pill every three days, which seems
pretty reasonable.

[10]: https://articulo.mercadolibre.com.ar/MLA-856867705-puritans-vitamin-d3-250-mcg-10000-iu-x-100-softgels-_JM
[11]: https://articulo.mercadolibre.com.ar/MLA-858947870-now-foods-vitamin-d-3-10000-iu-x-120-softgels-lo-mejor-_JM

Lower dosages tend to cost about the same per pill rather than per IU.
