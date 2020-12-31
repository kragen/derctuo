Suppose your exocortex’s radar detects a sniper’s bullet destined for
your head.  How soon does it need to detect this to yank you out of
harm’s way?

In a worst-case scenario, the bullet is aimed at the middle of your
chest, and it must jerk you either to the left or to the right by
200 mm to save you.  To avoid killing you in the process, it must
observe safe limits on the accelerations your body can handle; let’s
suppose 20 gees is a safe limit for horizontal accelerations.  20 gees
over 200 mm is a kinetic energy of some 39 J/kg or 8.8 m/s.  Your
time-average speed over those 200 mm is, however, only half of that,
or perhaps less if the acceleration is ramped up gradually: say,
4 m/s.  Thus some 50 milliseconds are needed; at typical sniper-rifle
muzzle velocities of 1000 m/s, this means the bullet must be detected
at most 50 meters away.

This is probably hopeless with on-body radar for flechette rounds, but
for better or worse, those have not been widely adopted.  Spitzer
bullets *have* been widely adopted, but perhaps from a 7.8-mm-diameter
bullet we can expect a radar cross section of 10 mm² or so anyway.  At
60 meters, this is about 3 nanosteradians, which I think is a
unidirectional path loss of 87 dB, so the reflection will be 174 dB
quieter than the emitted radar signal.  The exocortex would have to
detect this doppler-shifted -174-dB signal within a few milliseconds.
This is probably not feasible with conventional narrowband radars but
may be feasible with UWB pulse radar.

Possible alternatives include deploying radars tens of meters out, and
taking advantage of passive radar from 3G, Wi-Fi, and the like, so
that the path loss is only 87 dB.  From any point of view that isn’t
directly in front of the bullet, the radar cross section will be much
larger, and the bullet will have to come considerably closer to at
least some of the radars on its way to your head.

I’ve previously written on kragen-tol about “bulletproof hail” which
takes the alternative more Dagarti-ish approach of reacting to a
detected incoming bullet by swinging one of several candidate
“hailstones” of metal into position to directly block it.  This is an
easier approach in the sense that the hailstones can be constructed to
withstand much larger accelerations than 20 gees, and accelerating
them requires less force and energy than doing the same to your entire
body, so a much later detection time would be acceptable; the
hailstones could be initially positioned closer together than at
400-mm intervals in the sphere around your body; and it might pose
less risk of injury to you.  However, it also has the potential
disadvantages that such a system would be highly visible and could
also be used offensively, which might impose some practical obstacles
to its use.