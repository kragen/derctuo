I was thinking about how to reach high temperatures inexpensively and
safely during this quarantine.  Not, like, really high temperatures,
but hotter than the oven.

Carbon foam made by carbonizing bread is probably the easiest and most
accessible insulating refractory material for this kind of thing; it
doesn’t tolerate oxidizing conditions (it slowly burns above 700°),
but in reducing conditions it gradually converts to graphite, which
sublimes at [3642°][0].

[0]: https://en.wikipedia.org/wiki/Carbon

Stefan–Boltzmann temperatures
-----------------------------

“One sun”, the solar constant, is standardly approximated as
1000 W/m², which is the [Stefan–Boltzmann][2] emissivity of a black
body at 91.3°.  So a perfectly insulated object in full sunlight will
eventually heat up to 91.3°.  Because at that temperature all the
thermal radiation it emits is in the infrared, you can get it to heat
up to higher temperatures by painting it with paint that is highly
reflective in the infrared, or by putting infrared-reflecting glass in
front of it, but for simplicity I’m going to be considering the
blackbody case for now.

[2]: https://en.wikipedia.org/wiki/Stefan%E2%80%93Boltzmann_law

The 1368 W/m² on orbit corresponds to 121°.  “Two suns”, 2000 W/m²,
only corresponds to 160°, which is enough to cook, barely; you can
reach this level of illuminance with a single flat mirror.  Reaching
260° like this gas oven requires 4600 W/m², 4.6 suns, which is enough
for soldering electronics.  600°, enough to fire some red clays and
almost cast aluminum, emits 33 kW/m², 33 suns.  1000° is 150 suns,
1100° is 202 suns, 1600° (to melt quartz or pure iron) is 698 suns,
and 2072° (to melt sapphire) is 1715 suns.  Subliming graphite (3642°)
would probably be impractical at 13300 suns.  Quartic growth is a
bitch.  5500° (63000 suns) is the absolute limit.

Cavity absorbers
----------------

A small hole leading into a large cavity, sometimes called a cavity
absorber, behaves as a very good approximation of a blackbody, one you
can’t paint.  At low temperatures, convection of air is a significant
way to lose heat, but at the higher temperatures I’m interested in,
almost all the heat loss is through radiation.

Probably the smallest hole it’s practical to make in carbon foam and
concentrate sunlight through is about 10 microns in diameter.
Reaching 256 suns (1184°) then requires concentrating the sunlight
from a 256-times greater area on this hole: a circle of 0.16 mm in
diameter, for example, gathering about 20 microwatts.

The material inside the cavity mostly “sees” other material inside the
cavity; nothing short of a cat’s eye will send a significant fraction
of the light coming in the hole the hole directly back out the hole.
Almost all light that gets in needs to bounce around many times,
losing energy each time, before it can get back out.  So even the hole
in the top of an opened empty beer can looks black, even though the
beer can is 95%-reflective aluminum on the inside.

Unless the cavity is meter-scale or larger, parts of the cavity that
aren’t the hole need to be well insulated to prevent the loss of more
heat through conduction through the walls than from radiation through
the hole.

Optics of concentration
-----------------------

So if you can concentrate 256 suns on a 10-micron hole into a
sufficiently-well-insulated cavity, you should in theory be able to
heat it up to 1184° with those 20 microwatts.  This suggests that
solar furnaces can perhaps be made fairly small, though see below
about insulation thickness scaling.

It isn’t sufficient to focus the sunlight from an 0.16-mm-diameter
lens of any focal length whatsoever, though.  If the focal length of
the lens is too long, then the focused image of the sun will be too
large and therefore diffuse.  From the point of view of an ant passing
through the projected image, the whole lens is as bright as the sun,
but the lens is only a few times bigger than the sun from her point of
view, so the power density is not that high.  The f-stop of the lens
needs to be wide enough to get to 256 suns — specifically the lens
needs to look 16 times as wide as the sun, which is 0.53° (about 32’),
so the lens needs to subtend 8.53°, which means any lens with 256 suns
needs to have an aperture of f/6.72.  So if its focal length is 10 mm,
the lens needs to be at least 1.49 mm in diameter, at which point it
(like any other lens with a 10-mm focal length) will project an image
of the sun some 93 microns in diameter.  You can only get 256 suns
with an 0.16 mm diameter lens if its focal length is about 1.1 mm.

If you use a lens that’s bigger and further away — for example, the
10-mm-focal-length, 1.5-mm lens suggested above — then most of the
energy gathered by the lens will not enter the cavity.  A
93-micron-diameter sun image with a 10-micron hole in the middle of it
will gather about 100× as much energy as is actually put into the
cavity.  You might think that, in exchange, you don’t have to
constantly track the sun.  No such luck!  The Earth turns 360° per 24
hours, which is 0.25° per clock minute, so your sun image gets
displaced by a sun diameter every 2.1 minutes, whether that’s 10
microns or 90 microns.  (It’s slightly less when the sun is further
from the equator, but what’s important here is that it’s 2 minutes,
not 20 minutes or 2 hours.)

For lower concentrations, you can use a one-dimensional concentrator
like a solar trough (or a glass rod), running parallel to the sun’s
path in the sky, but reaching hundreds of suns that way is not
practical, though in theory it’s possible.

Non-imaging optics such as a compound parabolic concentrator are said
to improve the situation dramatically, permitting much wider input
angles.  You can use two developable compound parabolic concentrators
made of aluminum foil (reflectivity 95%) on cardboard, at right angles
to each other, to funnel light into the hole over a wider range of sun
angles; the disadvantage over using a CPC that is a solid of
revolution is that most of the light will be reflected from the
aluminum twice instead of once before going in the hole, thus reducing
efficiency.

The overall principle limiting the performance of NIO is conservation
of étendue: the intensity of illuminance times the angle it’s coming
from.  The thermodynamic limit is that you can’t use the sunlight to
heat things hotter than the sun’s surface (5500°); you would reach
that limit by arranging optics so that the poor ant sees solar surface
in every direction, 4π steradians of nuclear flaming death, 63000
suns†.  Conservation of étendue says that the reflection the ant sees
is only as bright as the sun, and you can only do that if all those
optics would direct any light the ant emits into some part of the
sun’s disc, which means that such optics necessarily have a very
narrow angle of acceptance: 2.1 minutes later, the ant’s remains will
see only cool blue sky.

So it seems like you ought to be able to shape the optics such that
you get 256 suns for 1/256 of the day before you have to reorient
them; any light emitted from the hole would be redirected onto the
sun’s daytime path.  Unfortunately, 1/256 of the day is only 5.625
minutes.  So this doesn’t help as much as you’d hope for these
ceramic-firing applications; you need to use feedback control.

5 suns, 271°, enough for soldering or baking, can be achieved by
optically coupling the hole to 4.8 hours of the sun’s path.  A
one-dimensional trough CPC focused on a slit might be adequate; four
flat mirrors spaced at angles around a hole might also work.

I’m not sure if I’m thinking this through correctly.  Sunlight on the
ground gives varying amounts of illuminance depending on the sun’s
angle; it’s only a whole sun at noon (and only twice a year at that,
and only if you’re in the tropics).  Sunlight reflected in a mirror
surely does look just as bright as the regular sun when you’re looking
at the mirror (from an angle where you can see the sun in the mirror,
anyway), but the mirror can be angled to spread it across a lot of
ground.

† this 63000 ought to be 4π steradians divided by however many
steradians the sun subtends, but I haven’t calculated that.

Insulation thickness scaling
----------------------------

Above I said that you can get your cavity to 1000° with 20 microwatts
of sunlight focused through a 10-micron hole if the cavity is well
enough insulated and you have good feedback control orienting the
reflector.  But it turns out to be impractical to insulate the cavity
well enough.

If your insulation material conducts heat at 0.3 W/m/K (typical for
[refractory bricks][1]), your cavity’s surface area is 6 cm², and you
have a 1000 K temperature difference, then at 1 mm of insulation
thickness you would lose 180 watts.  Not microwatts or milliwatts, but
entire watts.  So you’re seven orders of magnitude away from being
able to reach 1000° with a millimeter of insulation.  Exotic vacuum
panels might be able to gain you some of those orders of magnitude
back, but charred bread won’t.

[1]: https://www.traditionaloven.com/articles/81/insulating-fire-bricks

You can get maybe two or three of them back by making the cavity
smaller and the insulation thicker, but I think that at some point
it’s sort of a lost cause because once the heat diffuses a few
millimeters through the insulation it’s diffusing through a much
larger surface area again.  So microwatt-scale kilns would need
building-sized insulation.

A more practical approach is to scale up to, say, 100 watts, which is
about 320 mm × 320 mm of sunlight.  If you concentrate that down to
20 mm × 20 mm (or a 23-mm-diameter hole), you have your 256 suns.  The
hole can be at the end of a bit of a bottleneck leading into a chamber
of, say, 50 mm diameter, which is 65 mℓ and has a surface area of
7900 mm², 0.0079 m².  This would require 24 mm of insulation thickness
to lose the 100 W through conduction, so 100 mm or so should be
adequate to get it most of the way up to that temperature.  This ends
up being 250 mm in total diameter, which is probably about as big as I
can bake a loaf of bread.

So a solar furnace of subcentimeter total size probably isn’t
practical without vacuum multilayer insulation, but submeter is
totally feasible.

Insulation stops being a difficult problem with large cavities.
Consider scaling up by a factor of 200: a 10-meter-diameter cavity.
Let’s scale the hole up only by a factor of 50: it’s a
1.15-meter-diameter circle, swallowing 256 kilowatts fed to it by
hundreds of square meters of mirrors.  It holds 524'000 liters, and
its surface area is 314.15927 m².  To keep its conduction losses down
to 256 kilowatts, it only needs 400 mm of insulation!  Now the chamber
dwarfs the insulation; if you can dig it into the ground, you don’t
need any further insulation, although you might need to line it with a
sturdy refractory in case it turns the ground into lava.
