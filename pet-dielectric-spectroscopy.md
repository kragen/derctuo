Humidity sensors are useful.  Dielectric spectroscopy is easy with
modern microcontrollers.  PET bottles are widely available.  PET is
quite hygroscopic, and this alters its permittivity.  We can make
humidity sensors by dielectric spectroscopy of PET bottles.

Küchler, Farber, and Franck (2020, "Humidity and Temperature Effects
on the Dielectric Properties of PET Film") found 7–10% variation in
permittivity in 10°C PET films across the 1Hz–1kHz range when relative
humidity varied from 0% to 80%; dry PET film had a permittivity
magnitude of about 3.18–3.20ε₀, while PET film exposed to air at 80%
humidity was in the 3.40–3.45ε₀ range, depending on frequency.
Temperature also influenced the measurement; temperatures from 0°–65°
were all about the same, but the permittivity started to soar,
especially at lower frequencies, at 85° and above.  However, even at
the low temperatures where the permittivity magnitude was effectively
unchanged, the loss angle varied dramatically with temperature,
especially between 10 Hz and 10 kHz.

The frequency at which the peak loss occurred varied even more
dramatically with humidity than did the permittivity itself, varying
from about 100 mHz at 35% up to 100kHz at 80%; however, the curve they
plot seems somewhat irregular, though roughly exponential, and it's
not clear whether this is due to measurement imprecision or to it
having a complex shape.  The detection of such a peak may thus permit
a more precise measurement of humidity.

Such a sensor might drift over time, but it should at least be good
enough for a rough measurement of humidity and temperature, and it can
easily be made from garbage.

How slow would it be?  cloudevil on ##electronics pointed out that
this might be a problem.  The paper actually explains Fick's Law for
diffusion and did weight gain measurements.  They were using 23-micron
boPET and seem to have reached steady state after something like 10'.
This bottle I have here is more like 400–450 μm: I cut out a piece,
folded it in half four times, and it was about 7 mm thick, giving
about 440 μm per layer.  Extrapolating quadratically gives a time to
steady state of 40 hours.  This bottle is only partly biaxially
oriented, and probably less crystalline, so permeability might be
higher.  But it seems like it would be useful to use thinner plastic
to get faster response.  This wasn't a pressure bottle, but it's a bit
thicker than water bottles, and much thicker than chip bags.

This lubricating graphite powder I got at the hardware store (48 g
bottle labeled as 60 g) doesn't want to stick to the PET; I'm not sure
if abrasive will help or if stronger measures like plasma are
necessary.  A bit of scrubbing by hand with toothpaste was not
sufficient.
