Some notes on sodium silicate.

Nowadays sodium silicate, or waterglass, is principally employed in
foundries as a glue for sand-casting of metals, as a concrete sealant
against water, and as a grouting agent to solidify soft soils prior to
construction projects.  Such composites can, at best, be several times
stronger than ordinary concrete made with portland cement, and they
don’t suffer from the grey discoloration of portland cement or,
possibly, its carbon dioxide emissions.  I’m interested in its
possible uses for digital fabrication.

On Mercado Libre nowadays, companies like Geese Química are selling it
for [AR$140 per kg of “Silige” solution][m1], which is US$1.13 at [the
current AR$124/US$1 price][m2], and is probably about 400 g of sodium
silicate, thus working out to about US$2.80/kg.  This compares to
[AR$630 for 50 kg of portland cement][m3], US$5.10, or 10.2¢/kg.  Pure
white portland goes for about 50% more, and [hydraulic slaked lime is
AR$220 for 20 kg][m4], 3.5¢/kg.  Portland cement [is about 20% of the
weight of the final concrete][4], and [lime cement is about 25% of the
weight of the final mortar][5], while for a similar strength sodium
silicate can be 5% or less of the weight of the final solid; these
numbers work out to 0.88¢/kg for lime concrete, 2.04¢/kg for portland
concrete, and 14¢/kg for sodium-silicate-bonded concrete.  The price
of the aggregate closes the gap a little bit: construction sand costs
about 5¢/kg and gravel costs about 3¢/kg, though both are usually sold
by volume rather than weight.  So the total materials cost might be
5¢/kg for lime concrete, 6¢/kg for portland concrete, or 20¢/kg for
sodium-silicate concrete.

[m1]: https://articulo.mercadolibre.com.ar/MLA-850465717-silige-silicato-de-sodio-para-moldeo-de-fundicion-_JM?quantity=1
[m2]: https://www.cronista.com/MercadosOnline/dolar.html
[m3]: https://articulo.mercadolibre.com.ar/MLA-658949193-cemento-avellaneda-bolsa-x-50-kg-portland-en-oferta-_JM?quantity=1
[m4]: https://articulo.mercadolibre.com.ar/MLA-765654828-cal-comun-cacique-plus-o-extra-zona-norte-gba-_JM?quantity=1
[4]: http://matse1.matse.illinois.edu/concrete/bm.html
[5]: https://en.wikipedia.org/wiki/Lime_mortar

So, sodium-silicate-bonded concrete is about three or four times
pricier than portland-cement-bonded concrete when they are the same
strength.  This probably explains why portland is widely used as a
binder and waterglass is not.  But I think waterglass may have some
interesting advantages that can come into play with digital
fabrication.

If simply allowed to dry, sodium silicate takes a substantial amount
of time, and so it’s common to cure it with curing agents — in foundry
practice typically CO₂ gas, which can harden it within a few seconds,
but in other cases by mixing it with a curing agent, such as calcium
chloride or calcium hydroxide.

A lot of the existing literature on using waterglass as a binder
focuses on how to *slow down* the curing to minutes or hours, in order
to give it a long “pot life”.  But for digital fabrication, I think it
might be more interesting to explore how to *speed up* the curing,
ideally into the milliseconds to hundreds-of-milliseconds range.  Then
you could use it to “print” structures rapidly and with great freedom,
without having to wait hours for each part of the structure to
solidify before putting the next part in place.  But is this feasible?
How do we know the structures would be strong?  Would it be resistant
to weathering?  What would it look like — would it suffer from the
brutal, grim, gray appearance of typical portland concrete?  Can you
stick it to regular glass?

It turns out that they probably would be strong and resistant to
weathering, and they can have a wide variety of appearances, from
glass to sandstone and a variety of matte or glossy colors.  The
waterglass itself is transparent, although commonly a bit greenish due
to iron contamination.  And the possibility of structuring it at the
millimeter scale under digital control should make it possible to
achieve both stiffness and resilience dramatically better than that of
traditional concrete.

Other interesting attributes of waterglass
------------------------------------------

High-water-content waterglass is used as an intumescent
firestop — when heated above about 450°, the glass softens and its
water expands to steam, converting the solid, transparent, glassy
waterglass into a solid glassy opaque white foam.

[Waterglass is commonly used in pottery as a deflocculant][6], reducing the
viscosity of clay slips.

The tensile strength of waterglass-cemented composites can
significantly exceed that of ordinary portland concrete, and it has
been used as a binder for demanding applications like grinding wheels.

Chemical gardens grow in a waterglass medium; this suggests the speed
with which waterglass can be solidified if exposed to the right
reagents.

[6]: https://digitalfire.com/article/deflocculants%3A+a+detailed+overview

KEIM and mineral paints
-----------------------

One crucial question here for construction purposes is whether
waterglass can survive weathering — it’s no 

The Keim company in Germany, founded by Adolf Wilhelm Keim, has sold a
line of silicate-based “mineral paints” for over a century, and the
Bleeck company in the UK has recently begun selling a similar line in
the UK.  Keim has expanded to the UK and USA.  These paints are
principally based on potassium silicate as a binder, which is very
similar to sodium silicate, the principal difference being that solid
potassium silicate can be conveniently redissolved in water at room
temperature, while sodium silicate requires strong heating.  (Some
Keim paints instead use sodium aluminum silicate.)  These paints are
notable for their durability — 15 years is a common lifespan, but
[Keim claims that they have lasted over 130 years on the Stein Am
Rhein building][0], and that, although [“they will normally give 20–30
years satisfactory performance before redecoration is required,” it is
also the case that “There are many examples of Keim Mineral Paints
performing satisfactorily on lime render substrates for periods in
excess of 100 years.”][2].  I’m not sure whether these examples are
interior or exterior.

Their [Soldalit brochure][1] claims, “Color shades will not change for
decades,” and even recommends painting on top of acrylic or latex
paint to protect it from weathering “for decades”; Soldalit, unlike
their other paints, incorporates silica nanoparticles.

[0]: https://www.keim.com/en-gb/keim-library/longevity/
[1]: https://www.keim-usa.com/portals/0/app/clientresources/documents/BROCHURESOLDALIT%20USApdf.pdf
[2]: https://www.keim.com.au/comparison-of-keim-mineral-paints-and-limewash.html

[Wikipedia says][3], “The city hall in Schwyz and “Gasthaus Weißer
Adler” in Stein am Rhein (both in Switzerland) received their coats of
mineral paint in 1891, and facades in Oslo from 1895 or in Traunstein,
Germany from 1891.”

[3]: https://en.wikipedia.org/wiki/Silicate_mineral_paint

Although sodium silicate itself is water-soluble and will thus
redissolve in water, these paints “silicify” in contact with concrete
or masonry, forming covalently-bonded water-insoluble hydrophobic
products.

So all of this suggests that, in contact with calcite and quartz,
these soluble silicates form insoluble materials that will weather at
the rate of about the thickness of a coat of paint every 20 to 130
years.  This compares favorably to portland cement.

Curing by displacement
----------------------

Some sources talk about how calcium (hydr)oxide reacts slowly with
waterglass because of its low solubility in water (1.7 g/ℓ), and
magnesia (6.4 mg/ℓ), litharge (17 mg/ℓ), and minium (undetectably low)
do not excel it in this, though Vail (see below) reports that they all
cause “immediate precipitation”.  If we want to speed it further,
since the cations are apparently the active element here, more highly
soluble salts might be preferred — calcium chloride (750 g/ℓ) is
evidently standard, but other possibilities include magnesium chloride
(540 g/ℓ); Epsom salts, magnesium sulfate (270 g/ℓ); Norwegian
saltpeter, calcium nitrate (1200 g/ℓ); magnesium nitrate (710 g/ℓ);
aluminum hydroxide (100 mg/ℓ); aluminum acetate (soluble); alums such
as potassium aluminum sulfate (140 g/ℓ) or sodium aluminum sulfate
(210 g/ℓ); and neat aluminum sulfate (360 g/ℓ).  I’d rather not deal
with salts of lead, barium, strontium, cobalt, and so on, although
iron might be okay.

I guess these polyvalent cations displace the sodium cations,
increasing the degree of connectedness of the waterglass and thus
rapidly precipitating it.  It took me an embarrassingly long time to
figure this out.  (I’m preeetty sure aluminum will work for this too.)

What would be super awesome for this would be getting boron to form
soluble divalent or trivalent cations, but bor*ate* is of course an
anion; boron really likes to make covalent bonds, and most of the
compounds you’d hope would be soluble salts are instead found in [List
of highly toxic gases].

[List of highly toxic gases]: https://en.wikipedia.org/wiki/List_of_highly_toxic_gases

The various mineral species that ought to be formed include the
following.  The Mohs hardness of the minerals can be taken as some
kind of indication of the strength of bonding in the material, but
since the materials being formed here are actually amorphous, it is
technically incorrect to refer to them as *being* these minerals; the
amorphous glass will have different characteristics, including
hardness, density, thermal behavior, and perhaps even color.

* [Calcium silicate]s: in the 2:1 Ca:Si ratio, this is the “[belite]”
  giving Portland cement its late strength, or “[larnite]” (Mohs
  hardness 6) in the wild.  This is also called “lime olivine”,
  although properly speaking olivine varies from [forsterite]
  (Mg₂SiO₄, Mohs 7, including [peridot], a refractory melting around
  1900°) to [fayalite] (Fe₂SiO₄, Mohs 6.5–7).  Halfway-lime olivine is
  the rare [monticellite] (CaMgSiO₄, Mohs 5.5).  [Tricalcium
  silicate], with a 3:1 Ca:Si ratio, is [alite], which I think is
  weaker and tends to revert to belite and lime; in the 1:1 Ca:Si
  ratio we have [wollastonite] (CaSiO₃, Mohs 4.5–5, melting at 1540°),
  noted for its whiteness and used as a filler in plastics, paint, and
  ceramics; it tends to form long acicular crystals when allowed to
  crystallize.

* [Magnesium silicate]: as mentioned above, in the 2:1 Mg:Si ratio,
  this is forsterite olivine.

    I worry somewhat about olivines’ vulnerability to weathering,
    since in an amorphous gel they will be even more exposed to
    reactions.  But the way olivines weather is by incorporating
    water, as with [iddingsite] (Mohs 3).  If hydroxyls are just
    incorporated into the olivine structure, you may get [humite]
    (Mohs 6–6.5), [norbergite] (Mohs 6–6.5), [chondrodite] (Mohs
    6–6.5), and [clinohumite] (Mohs 6).

* [Manganese silicate]: this is the heavy mineral [tephroite], Mohs
  hardness 6, which exists in a continuum with forsterite and
  fayalite.

* [Aluminum silicate]: this occurs naturally as [topaz], Mohs hardness
  8, although I’m not sure whether you can make topaz without
  fluorine, but also as several other minerals.

    Topaz (Al₂SiO₄(OH,F)₂)has a 2:1 Al:Si ratio; other aluminum
    silicate minerals with the same ratio include [andalusite],
    [kyanite], and [sillimanite], which are polymorphs of Al₂SiO₅.
    Kyanite, commonly used as a refractory, is the thermodynamically
    favored form at STP, and it's highly anisotropic, with Mohs
    hardness of 4.5–5 along one crystal axis and 6.5–7 perpendicular
    to it; it can be cooked into mullite and vitreous silica at 1100°.
    Sillimanite is Mohs 7 and andalusite, also commonly used as a
    refractory, is 6.5–7.5.

    [Kaolinite] (Al₂Si₂O₅(OH)₄) has a 1:1 Al:Si ratio; it is a
    phyllosilicate clay, with almost negligible strength.  Heating it
    above 550° converts it to [metakaolin], a tranformation that is
    complete at 900°: Al₂Si₂O₇; this is used as an excellent pozzolan
    for pozzolanic cement, but it is still fragile.  Further heating
    converts it into Si₃Al₄O₁₂ + SiO₂, quartz and a sort of spinel,
    above 950°; to platelet [mullite] 2(3 Al₂O₃ + 2 SiO₂) and
    [cristobalite]; at to acicular mullite (contaminated with the
    cristobalite) above 1400°, which remains solid up to 1840°.

    Mullite itself — the key to the alchemists' famous Hessian
    crucibles — can also form at 3:2 or 2:1 ratios, but I suspect that
    isn’t what you’ll get by treating sodium silicate with aluminum
    salts.

[mullite]: https://en.wikipedia.org/wiki/Mullite
[cristobalite]: https://en.wikipedia.org/wiki/Cristobalite
[metakaolin]: https://en.wikipedia.org/wiki/Metakaolin
[Kaolinite]: https://en.wikipedia.org/wiki/Kaolinite
[sillimanite]: https://en.wikipedia.org/wiki/Sillimanite
[kyanite]: https://en.wikipedia.org/wiki/Kyanite
[andalusite]: https://en.wikipedia.org/wiki/Andalusite
[topaz]: https://en.wikipedia.org/wiki/Topaz
[Aluminum silicate]: https://en.wikipedia.org/wiki/Aluminum_silicate
[iddingsite]: https://en.wikipedia.org/wiki/Iddingsite
[clinohumite]: https://en.wikipedia.org/wiki/Clinohumite
[chondrodite]: https://en.wikipedia.org/wiki/Chondrodite
[norbergite]: https://en.wikipedia.org/wiki/Norbergite
[humite]: https://en.wikipedia.org/wiki/Humite
[tephroite]: https://en.wikipedia.org/wiki/!tephroite
[Manganese silicate]: https://en.wikipedia.org/wiki/Manganese_silicate
[Magnesium silicate]: https://en.wikipedia.org/wiki/Magnesium_silicate
[wollastonite]: https://en.wikipedia.org/wiki/Wollastonite
[alite]: https://en.wikipedia.org/wiki/Alite
[Tricalcium silicate]: https://en.wikipedia.org/wiki/Tricalcium_silicate
[monticellite]: https://en.wikipedia.org/wiki/Monticellite
[fayalite]: https://en.wikipedia.org/wiki/Fayalite
[peridot]: https://en.wikipedia.org/wiki/Peridot
[forsterite]: https://en.wikipedia.org/wiki/Forsterite
[dicalcium silicate]: https://en.wikipedia.org/wiki/Dicalcium_silicate
[larnite]: https://en.wikipedia.org/wiki/Larnite
[belite]: https://en.wikipedia.org/wiki/Belite
[Calcium silicate]: https://en.wikipedia.org/wiki/Calcium_silicate

Notes on existing research
--------------------------

Sodium silicate is a bit of a tricky beast to find good engineering
data about, because it exists as a continuous spectrum between pure
lye and pure fused silica, with a highly variable amount of water, and
additionally can react with gases from the air as it hardens.

### Gonzalez 2007 ###

“Behavior of a sodium silicate grouted sand” by Gonzalez and
Vipulanandan, 2007.  Mixed “N-Sodium Silicate” (“Na₂SiO”(!!)·3H₂O)
with “dimethyl ester” (which ester?  “C₁₀H₁₀O₄” — clearly these are
not organic chemists — “a byproduct of the nylon industry” — oh,
apparently it’s a random mixture of succinate, “gluterate”
(glutarate?), and adipate?) and injected it into “medium dense sand”
to grout it in a mold.  Compressive strength of the sand was 300–1900
kPa, Young’s modulus 200–500 MPa, but it had creep.  No explanation is
given as to why they thought adding dimethyl esters would be
interesting, but apparently they sped up the gelling, maybe as a
source of CO₂, but weakened the final product.  Strain at failure was
0.4%–2%.  No samples without DME were included.  No tensile or
flexural strengths were recorded, I guess because they were interested
in grouting sands for civil engineering purposes.

I have zero faith in Gonzalez and Vipulanandan; the formula they give
for “sodium silicate” would actually be a metallic silicon-sodium
alloy which would be at the very least violently reactive with water
and possibly pyrophoric.  The absence of a DME-free control is
particularly glaring (for my purposes) and they don’t talk at all
about their CO₂-control measures.

### Zhao 2011 ###

“Nanoindentation and Brillouin light scattering studies of elastic
moduli of sodium silicate glasses” by Zhao et al., 2011.  Talks about
a “large discrepancy” in Young’s modulus measured by different methods
(and offers an explanation).  They prepared their sodium silicate with
varying amounts of sodium (8, 20, 30, and 40 mol%) from Na₂CO₃ and
SiO₂ (presumably crystalline) mixed in an agate pestle and then melted
at 1500° or, for the 8mol%-Na glass, 1700°, and compared to fused
silica.  The idea is, I guess, that the sodium carbonate converts to
Na₂O when you heat it up.

A thing I’m not clear about with these mole percentages is whether the
metals are 8 mol% Na — thus, two sodium atoms per 23 silicon
atoms — or whether the oxides are 8 mol% Na₂O — thus, two Na₂O units
per 23 SiO₂ units, and therefore *four* sodium atoms per 23 silicon
atoms.  I’m pretty sure it isn’t two sodium atoms per 23 silicon *or
oxygen* atoms.

Astonishingly, they got plastic deformation out of the glasses by
indenting it with a diamond-tipped “Hysitron TI 900 TriboIndenter”,
which they then measured with an AFM.  The whole methods section of
the paper is equipment porno.

They got a 72 GPa Young’s modulus for fused quartz with all four
measurement methods, down to 67 GPa at 8%, 61 GPa at 20%, 61 GPa at
30%, and about 59 GPa at 40%.  There’s a bunch of stuff in there about
correcting the figures because at the higher sodium contents they give
significantly different results, up to 64 GPa for nanoindentation for
the 40%.

They also give a “hardness” value in GPa, ranging from 8 GPa for fused
quartz down to 4–4.5 GPa for the 40% sodium glass.  I’m guessing that
this is the compressive yield stress, although I am surprised to learn
that these glasses *have* a yield stress; I thought they would just
deform elastically until they broke.  But I guess in a small enough
area you wouldn’t have enough energy to propagate a crack, and so even
if the glass there powdered, you’d squish it back into the glass
surface (“indentation-induced densification”, although it’s not clear
that there was any powdering going on).  I don’t know.  The AFM images
make it look pretty fucking rough, and in the glasses with larger
amounts of sodium, there’s a “pile-up” of plastically deformed
material around the outside of the four-micron-wide triangular
craters.  But in the lower-sodium glasses, the surface is totally flat
outside the craters.

No tensile strength figures are given.

### Redwine 1967 ###

“The Eﬀect of Microstructure on the Physical Properties of Glasses in
the Sodium Silicate System”, by Redwine and Field 1967.  It’s not a
survey paper — it focuses on changes in physical properties that can
be obtained by heat-treating glasses within a metastably-miscible
concentration range — but it still gives a broader overview of the
field.  It gives values of Young’s modulus *E* from 8.38–9.36 million
psi (57.8–64.5 GPa in non-medieval units) depending on temperature,
composition, and heat treatment, as well as measured values of shear
modulus *G* (25–27 GPa), bulk modulus *B* (33–36 GPa), and Poisson’s
ratio *μ* (0.18–0.20).  Linear TCE ranged from 4.64 ppm/° to 10.15
ppm/°.  No strength of any kind is measured.  Most of the paper is
concerned with how these vary by temperature.

They don’t seem to say how they made the glasses.

It suggests that at low temperatures Na₂O and SiO₂ are miscible at
below about 77 mol% Si₂O and above about 97 mol% SiO₂, but between
these limits there is a regime where the two materials spontaneously
separate into different phases, presumably a sodium-rich phase and a
silicon-rich phase.  This immiscibility persists up to about 825°,
above which they are miscible in all proportions.  (The plot only goes
down to 500°, though, perhaps because below that temperature the
separation processes are too slow to observe.)

Mostly they focus on glasses of 7.2 mol% to 18.4 mol% Na₂O, which is
to say, between 92.8 mol% SiO₂ and 81.6 mol% Si₂O, thus covering much
of the range where this immiscibility occurs.  Within the “unstable”
region, they report that heat treatment resulted in phase separation
into “two independently interconnected phases”, while in the
“metastable” region it resulted in “classical nucleation and growth of
particles”.

(Interestingly, the miscibility limit in this paper seems close to the
“pile-up” limit displayed in Zhao 2011 above.  This might be a
coincidence.)

It might be interesting to see if laser heat treatment could induce
this “heat treatment” effect in very small areas very quickly, as a
way of writing data; for compositions right in the middle of the
“unstable” region, say around 11 mol% Na₂O, the separation might be
fastest.  However, in the paper, they heat-treated for 1½ hours at
770° to get phase separation at 12.6 mol% Na₂O, so that might be very
challenging.  However, they noted that they were not able to obtain
homogeneous glasses for some compositions, presumably because they
could not cool them fast enough.

They measured the “dilatometric softening point” of the glasses from
500° for the highest-sodium variants (18.4 mol%) up to 735° for a
heat-treated high-silica glass (7.2 mol% Na); this is the temperature
at which heating the glasses does not dilate your dilatometer any
further because the viscosity is low enough that it flows instead,
which is of course dependent on how much force the dilatometer is
clamping with.

The linear coefficients of thermal expansion
(<sup>α</sup>R*T*-350)ranged from 4.64 ppm/° for heat-treated 7.2-mol%
Na glass up to 10.15 ppm/° for 18.4-mol% Na without heat treatment,
varying linearly.  These numbers barely changed with heat treatment.

### Ito 1982 ###

“Dynamic Fatigue of Sodium-Silicate Glasses With High Water Content”,
by Ito and Tomozawa, 1982.  These guys were also at RPI.  They
measured 40–70 GPa Young’s modulus for dry sodium silicate and 3–50
GPa for glasses including a lot of water.  They also measured its
tensile strength but I can’t understand their results.

They slowly (over several days) dried out some commercial sodium
silicate solution (8.9 wt% Na₂O, 28.7 wt% SiO₂, Na₂O·3.3SiO₂, which I
guess is 23.2 mol% Na₂O) to various water contents around 25%, at
which point it was solid; they sliced it into 1.7-mm-thick slips and
and used four-point bending to measure its flexural strength, finding
a strong dependence on speed of loading especially for
higher-water-content glasses, which also had the highest Young’s
modulus, which was, insanely, *viscoelastic*.

Unfortunately the Y-axis labels on the fracture strength plots are
very difficult to understand: it says “Log Fracture Strength
(kg/mm²)”, which is already ambiguous (is that a base-10 log or
base-*e*?) but to worsen the situation, a legend helpfully explains:
“log *σ* = (1/(n+1)) log *σ̇* + log *C*”, only without the parentheses.
Is that an empirical approximation formula or does it explain how the
plotted numbers were derived?  The numbers plotted, at any rate, range
from about -0.1 to about 1.2, with the strongest glass typically being
the one with 15.9% water, which is slightly stronger than the dry
glass.  If we suppose that this is a base-10 logarithm of the flexural
strength, then we have a tensile strength of about 0.8–16 kg/mm², or
8–160 MPa in modern units.  But I am not confident in that
interpretation.

The Young’s-modulus plot in Fig. 4 is, by contrast, decently
labeled — it uses a logarithmic Y-axis but with ticks labeled in real
units.  It gives 4–7 thousand kg/mm² (40–70 GPa) for the dry glass,
with numbers ranging from 0.3–5 (3–50 GPa) for the wet glasses.

Their figure 5 also plots Young’s modulus, a theoretical Young’s
modulus limit at infinite stress rate, which is some three orders of
magnitude lower, ranging from 1 kg/mm² to 5.5 kg/mm².  I suspect they
have mislabeled their plot.

They also plotted the Knoop hardness of the samples, in the range
50–400 kg/mm² (500–4000 MPa), decreasing with higher water content.

They cite “McMillan (1982)” as giving flexural strengths for soda-lime
silica glass, which looks like a paper in “Non-Crystalline Solids” by
McMillan and Chelebik, 1980, I think volume 38/39, p. 509.  I think
that’s actually *Chlebik*, and the paper is perhaps “The effect of
hydroxyl ion content on the mechanical and other properties of
soda-lime-silica glass”.  But it seems like probably that paper
doesn’t cover soda-silica glass.  (And they didn’t say it did, after
all.)

### Medina 2009 ###

This article has the deeply misleading title, “Water Glass as
Hydrophobic and Flame Retardant Additive for Natural Fibre Reinforced
Composites,” by Medina and Schledjewski, 2009.  I say “deeply
misleading” because waterglass is preeettty faaar from being
hydrophobic!  As noted above, drying the stuff out is really tough.

The article has a lot of problems like that.  It describes a Si(OH)₄
moiety as “silane”, talks about "natural fibers" as if they're all
equivalent of (I was assuming cellulose because the descriptions they
give don’t fit chitin, keratin, asbestos, etc., but even if it’s
cellulose not all cellulose is the same — finally on page 3 we find
out that the fiber they tested is 70% kenaf, 30% hemp, with no source
given), never describes which acrylic resin it’s using (I think,
although sometimes it mentions “polyester”, so maybe it’s a polyester
acrylic — although on page 8 they finally slip up and admit that it’s
one of the Acrodurs, whose composition is apparently secret), never
describes how much sodium is in the waterglass it’s using, uses a very
crude flammability test, etc., etc.

But it’s pretty interesting.  Apparently they glued together some
cellulose fiber mats with various mixtures of sodium-silicate
waterglass and the unspecified acrylic resin, and got some decent
boards out of it, and of course the waterglass made them flame
retardant.

Because of the amount of crucial data omitted, apparently
intentionally (“a new water glass type specially developed as
hydrophobic additive for acrylic systems”), the paper falls far short
of basic reproducibility criteria.

### Fused quartz properties ###

The low-sodium endmember of the sodium silicate continuum is fused
quartz, and that’s the most highly polymerized part, so we would
expect all sodium silicates to have tensile strength and hardness at
most that of fused quartz.

<http://www.quartz.com/gedata.html> agrees with
<https://technicalglass.com/technical_properties/> on the curiously
precise tensile-strength number of 48 MPa.  Marijuana paraphernalia
merchant
<https://highlyeducatedti.com/blogs/information/thermal-shock-vs-tensile-strength>
gives 67 MPa for flexural strength and 50 MPa for ultimate tensile
strength, apparently quoting makeitfrom.  It also gives 0.5 ppm/°
linear TCE.

### Stachowicz 2010 ###

“Studies on the Possibility of More Effective Use of Water Glass
Thanks to Application of Selected Methods of Hardening”, by
Stachowicz, Granat, and Nowak, 2010.  They say that waterglass-bound
foundry casting sand commonly has tensile strengths (Rₘ<sup>U</sup>)
in the 0.3–0.5 MPa range; with 5% waterglass in their sand they got
tensile strengths as high as 3.6 MPa, with higher-sodium waterglasses
generally giving stronger bonds.

They’re concerned with binding foundry sand with small amounts
(1.5–5.0%) of waterglass, and in particular with whether microwave
heating can make it stronger and maybe allow you to use less than the
usual minimum of 2.5%, which it apparently does.  Also they were able
to microwave their samples for four minutes instead of oven-drying
them for two hours.

It has a helpful table of waterglass grades used in foundries, with
molar ratios of SiO₂ to Na₂O anging from 3.2:3.4 (grade 137) to
1.9:2.1 (grade 150).

I’m not sure whether their 1.5% and 5% etc. refer to the weight of the
dried waterglass or to its wet weight.  (Grade 137 is 35% solids, with
the rest being water, while the very viscous grade 140 is 42.5%
solids.)  Anyway, the strength continues to increase quite linearly up
to the 5% they tested, which makes me optimistic that strengths
several times higher are feasible with higher binder content.

The linear extrapolation of the 1.5%–5% suggests a tensile strength of
something like 50–70 MPa for solid 100% waterglass, which is consonant
with my tentative 8–160 MPa interpretation of Ito 1982 and the 50–70
MPa numbers given above for fused quartz.

Carbon dioxide is not mentioned.

All nine entries in their bibliography are Polish.

### MacKenzie 1991 ###

“Silicate Bonding of Inorganic Materials, Part I”, by MacKenzie et
al., 1991.

XXX

### Vail 1952 ###

“Soluble Silicates: Their Properties and Uses”, Vail, 1952.  This is a
thousand-page two-volume set full of valuable information.

It mentions that a major use of waterglass in the mid-1800s was “the
hardening of stone to increase its weather resistance”, further
allaying my concerns about weathering, and it has a whole section on
using it to bond grinding wheels.  It mentions that Feuchtwanger
claims to have introduced the use of waterglass in the US, using it to
prevent rusting of naval weaponry.

It seems that when Vail wrote his book, sodium silicate was
considerably more widely used than it is today: “There are few
manufacturing plants which do not make some use of [soluble
silicates].”  Today I think it’s kind of a niche product, despite the
growing importance of avoiding phosphate runoff (silicates can
substitute for phosphates as detergents).  This consideration does not
appear in the introductory section, although it does talk about how
conservation may stimulate the use of silicates in the future.

With respect to the prospect of precipitating or “curing” waterglass,
Chapter 2 (“Present Practices”) begins wih the promising note: “Most
of the impurities likely to be found in sand form insoluble silicates,
and even small quantities, less than one per cent, can create serious
difficulties.”  It has the appealing note that the old way of making
it was “dissolving diatomaceous earth in caustic liquors”, which does
sound much easier than the standard approach of heating sulfate or
carbonate of soda to some 700° to 800° in contact with sand.  On the
other hand, the standard approach is considerably more legal in
Argentina.

It explains that the “so-called neutral glass”, usually “pale bluish
or greenish”, is 1:3.3 Na₂O:SiO₂, although IIRC the pH of the solution
is still above 11, while the “alkaline” is 1:2.1.  This probably
explains why the pale greenish bottle I have doesn't burn my skin and
was sold as “neutral”.

Astoundingly, at this time it was still not known that solid
waterglass, or indeed any solid, was amorphous!  Vail says the
question “might be of more academic than practical value”, though he
also said, “A sodium silicate is as nearly devoid of ordered structure
as any known material.”

It explains that finely divided dry waterglass sometimes *does* get
dissolved in water at atmospheric pressure 100°, but to dissolve lumps
of glass, 90–100 “pounds gage” steam pressure is used (psig I guess,
so 700–800 kPa absolute).

It explains that the reason sodium silicate has eclipsed potassium
silicate is just that sodium is cheaper than potassium.

I find this unjustifiably amusing: “Immediately after use, hydrometers
should be washed thoroughly with warm water until alkali cannot be
tasted on the glass...” — clearly a pre-OSHA book.

He points out that you can blow waterglasses just like you can blow
other glasses, but that it can contain varying amounts of water
“without substantially altering their appearance”.  This makes me
wonder if they might be a particularly suitable material to attempt to
3-D print graded-index optics in.

It explains that alcohol precipitates waterglass just by removing
water, which I had suspected but was not sure of.  Also, he mentions
doing the same with alkali metal salts or ammonia.

It includes the oldest citation I've seen: “A sodium silicate glaze is
described in cuneiform records of the reign of Ashurbanipal, 668–626
B.C.: 10 mana of sand, 10 mana of alkali ash, and 1.67 mana of styrax
gum were heated to white heat, cooled, crushed, and placed in a clean
melting pot in a cold furnace.”

A surprising thing mentioned a couple of times in the book is that
potassium silicate does not effloresce, while sodium silicate does, a
fact particularly relevant for production of fake stone; this
afflicted Ransome’s fake stone in 1861.

A technique frequently mentioned both in this book and in Keim's paint
brochures is the inclusion of amorphous silica particles in the
liquid — a sol of precipitated silica gel particles, for example,
although diatomaceous earth should also work.  This reduces the amount
of the waterglass that must be gelled to form a solid gel, since the
particles form part of the gel network.  Other effects include
thickening the liquid and making it colloidal and possibly
thixotropic.

In Chapter 5, Vail refers to “immediate precipitation which occurs
when calcium, magnesium, or lead oxides are mixed with concentrated
silicate solutions”, although it's not clear what timescale he’s
talking about.
