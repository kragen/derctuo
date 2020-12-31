Home device networking, sometimes called home automation, is elusive,
despite predictions of "home computers" going back to at least Gordon
Moore's "Cramming" article in 1965, and Ray Bradbury's speculation in
his 1950 story "There Will Come Soft Rains".  X-10 powerline signaling
was developed in 1975 and on sale in Radio Shack and Sears in 1978; it
was affordable by the late 1980s.  Despite this, home device
networking mostly remains a novelty, very unlike, for example,
networking and automation in industrial manufacturing.  Automation has
crept into the dwellings of the humans mostly in non-networked forms:
toilet tank float valves; washing machines; dishwashers; thermostats
on the air conditioner, furnace, and hot water heater; timers on
lights; shuffle on the CD player.  Now in 2020 home illumination is
beginning to be automated in earnest, with multiple competing brands
of color-changing LED lightbulbs being used by criminals to launch
distributed denial of service attacks.

Still, though, the majority of the house remains stubbornly
unmechanized.  In my view, this is largely the fruit of twelve
millennia of building houses to remain habitable despite being
entirely passive.

Water
-----

This leads to some drawbacks.  Consider how smart plumbing might work.
A small pressurized tank under your sink provides immediate
availability of high-volume water at whatever pressure you choose; the
tank refills at leisure by requesting water from upstream, which can
come by way of a trickle sized for average flow rather than maximum
flow.  A shower, for example, might use 15 liters per minute (250
ml/s) for half an hour per day, for an average of 5 ml/s, 50 times
less.

This trickle could travel through a tiny pipe the size of a coffee
stirrer, but alternatives include miniature aqueducts, greenhouse
groundwater filtered through sand, a tiny fountain trickling down the
bathroom wall, and a mobile broom-shaped robot with a bucket.  None of
these are prone to the catastrophic failure modes that characterize
traditional high-pressure in-wall water pipes, such as flooding your
basement and destroying everything stored there, Fantasia aside.

Thermal and humidity control
----------------------------

Michael Reynolds's Earthships are designed to permit passive climate
control by way of a thermosiphon-driven seasonal thermal store
consisting of an earth berm somewhat larger than the house itself with
cooling pipes running through it.  The greenhouse section of the
house, with near 100% glazing and facing the equator, is heated by the
sun.  To heat the house's living spaces, the cooling tubes are closed
and the doors to the greenhouse are opened, permitting natural
convection to carry the heat into them; to cool the living spaces, the
cooling tubes and the greenhouse's skylight are opened, so that
natural convection carries hot air out of the greenhouse, which is
replaced by air that passes through the cooling tubes into the living
spaces, from which it passes into the greenhouse with difficulty past
the closed doors.

Reynolds and typical hippieish Earthship buyers see the manual opening
and closing of the skylight and cooling tubes as an extra benefit: it
keeps the residents in touch with the natural climate, and because of
the large thermal mass of the walls, floor, and roof of the living
spaces, it's rarely needed even for comfort, and never for simple
safety.  But more sophisticated redundant thermal homeostasis systems,
trading off different candidate thermal stores based on remaining
reserves and predictions of future weather, could probably do the same
job without all the expensive earthmoving.

For example, when plenty of energy is stored in the house's batteries
due to recent sun, or especially when they are already full and the
sun is still shining, it might be essentially free to run a
vapor-compression or ammonia-absorption heat pump to top up
thermal-mass or phase-change reserves of either heat, cold, or both,
or to directly heat or cool the living spaces.  When heating the
living space, the cold from the heat pump's evaporator (or
corresponding part) might be more efficiently stored in a cold thermal
reservoir instead of vented to the outside air.

Cold reservoirs above the house and hot reservoirs below it permit the
natural convection of air through butterfly valves like those in a
car's throttle, which consume energy only when their setting is being
changed; this permits not only manual system operation in the case of
a power failure but also fail-safe measures where, if nobody is home,
a default thermal coupling to the reservoirs keeps expected
temperature swings within safe limits.  Such reservoirs can in many
cases do double duty as drinking-water cisterns.

When the reservoirs run low, or an inability to replenish them for a
long time is predicted, such active heat pumping can also reduce the
drain on their thermal stores for future use.  Also, house ventilation
to the outside through countercurrent or regenerative heat exchangers,
which costs some heat or cool as well as humidity control, can be
diminished in exchange for increased use of forced-air HEPA
filters — a measure also warranted when outdoor air quality is poor,
for example during the acoughalyptic wildfires ravaging California and
Washington as I write this.

And, of course, when trying to keep the indoor temperature from
rising, lowering equatorial-facing awnings to shade windows, and
raising polar-facing awnings to unshade them, is another possible
alternative, which must be traded off against reduced garden growth
and glossy LCD visibility, on the bright side, and reduced
illumination and increased human depression, on the dark side.  This
is also sensitive to time of day: east is "equatorial" in the morning,
"polar" in the afternoon, and west vice versa, while both are super
"polar" at night.

Another tradeoff that can be made is the evaporation of collected
rainwater in rooftop tanks to reject stored heat, especially at night,
with a fan, or when it's windy; or the heating of water in passive
solar collectors to acquire stored heat.  Such passive solar
collectors can do double duty as awnings; such evaporation tanks can
do double duty as swimming pools.  A cooling tower, indoors or
outdoors, is also a fun place for kids to play in the spray on a hot
day.

Different kinds of thermal stores may require different quantities of
resources and have different "self-discharge rates" as well as
different impulse responses.  Water at 0° represents a cool resource
of some 100kJ/kg when the objective temperature is 23°, and can be
stored in a pit lined with geomembrane, possibly surrounded and topped
with some kind of insulation.  Ice at 0° represents an additional cool
resource of another 333kJ/kg when the objective temperature is
anything above 0°, so you can quadruple the density of your storage if
you can reach a temperature below 0°.  A bunch of thin coolant pipes
running through a trench in a yard, a few centimeters apart, can heat
and cool the soil in a cylinder a few centimeters around them as a
daily thermal store; but if they are instead a couple of meters apart
and deep, they can also heat and cool the soil a few meters around
them as a seasonal thermal store.  These stores can be orders of
magnitude larger than water tanks of the same cost, but their
available heat flux is lower.

Other phase-change materials that could be useful in this context
include Glauber's salt — sodium sulfate decahydrate, which melts at
32°, yielding 252 kJ/kg — and a eutectic of NaCl and Glauber's salt,
which melts at 18°, yielding 286 kJ/kg.  Glauber's salt can provide a
very compact high-temperature reservoir, while the eutectic with
sodium chloride can provide a low-temperature reservoir which, though
it provides less heat capacity than water, can be operated at a much
more convenient temperature.  Glauber's salt costs some US$0.05/kg,
some fifty times as much as water, but a 10-megajoule heat or cool
reservoir — roughly a person-day with perfect insulation — adds some
US$2 to the materials cost.

For cooking or hot-water heating, it may be worthwhile to use a
higher-temperature thermal store than Glauber's salt can provide — for
example, a pebble bed of stones or ceramic beads over which air is
passed, or a phase-change reservoir of sulfur that melts at 115°
yielding 54 kJ/kg, or of the famous "solar salt", a eutectic that
melts at 220°.

Even simple conversion of battery energy into heat may have a role;
the wattage of the active heat pump will inevitably be limited,
perhaps to a kilowatt or so, but there is no need for such a limit on
resistive heating.  I bought a 600-watt nichrome resistor for boiling
water a few weeks ago for US$1, complete with shitty power cable and
shitty plastic protective cage and ceramic form.

So the climate control system, responding to human commands, has many
available tradeoffs.  Among others, it can spend water to get cool;
spend battery to get cool, heat, or both; spend battery to generate
light; spend light to get cool; spend water and air dryness to get
cool, or spend heat and water to raise air humidity; spend battery to
get air purification; spend heat or cool, depending on the outdoor
temperature and pollution level, and possibly air humidity or dryness,
to get air purification; and spend cool to generate light and garden
growth.  (This is not counting the small amount of battery needed to
pump air and water around and operate valves.)

By using active control of these tradeoffs with cybernetics, optimal
control theory, Bayesian modeling of future climate, and Black–Scholes
option theory, it should be possible to achieve Earthship-like comfort
and security without the orders-of-magnitude overprovisioning that
makes the Earthship design so expensive.
