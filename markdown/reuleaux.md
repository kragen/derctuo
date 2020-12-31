I’m reading Reuleaux’s 1874 book on machine kinematics, or rather its
English translation, and I thought I’d make some notes.  Reuleaux
formulated kinematics as it is studied today, in terms of lower and
higher kinematic pairs with varying degrees of freedom, such as
revolute, prismatic, and cylindrical joints.

This is one of the rare cases where the Google scan is of less bad
quality than the Archive’s own scan; kinematicsofmach00reulrich.pdf
has the left side of many pages cut off, while so far Google’s
kinematicsmachi01reulgoog.pdf seems okay.  As always it is lower in
resolution and color depth, but so far this seems to be an
improvement, accelerating as it does the rendering.  It seems to lack
OCR text, though, while the Archive’s own scan has somewhat passable
OCR.  The OCR is still not good enough to copy and paste, but
sometimes it may be faster to edit the results than to retype from
scratch.

The Archive’s scan also does a less appallingly bad job of capturing
the figures, though it still leaves something to be desired.  (Compare
p. 31, for example; 49 of 646 in the Archive’s scan, 52 of 651 in
Google’s.)

Mupdf in particular handles the total absence of OCR text rather
disappointingly when you initiate a search, freezing for a few minutes
before it comes to the end of the document.

Many aspects of the typography and usage of this work deviate from
modern standards, but none more glaringly than its use of increased
letter spacing for emphasis, where modern practice, or even common
19th-century English practice, would use italic.  This was of course
necessary in German blackletter typesetting, which entirely lacks
italic, but I suspect that even the original German version of this
work was set in modern humanist letters rather than blackletter.
(Perhaps I should go back and check.)  I often fail to notice this on
the first reading of a passage.

Where I’ve quoted passages using this form of emphasis below, I’ve
replaced it with italics.

The typography *does* use italics to distinguish mathematical
variables and references to points in figures.

Introduction
------------

It’s interesting that Reuleaux traces the development of kinematics
though a whole series of 19th-century researchers, including (to my
surprise) Ampère.  I had sort of thought that the systematic study of
kinematics had sort of been stuck at the level of Archimedes’ “simple
machines” still taught in the vulgar elementary schools: the inclined
plane, the screw, the lever, the wheel and axle, the pulley, and the
wedge.  But indeed Reuleaux outlines the development of the ideas by
Galileo, by Hâchette, by Borgnis, by Lanz, by Monge, by Willis, by
Ampère, by Newcomen, by Watt, and by a dozen others, prior to himself.
Justifiably, though he characterizes all their theories as “wrecked”.

It’s intriguing to see the etymology and sense development of
“automatic” and “automation”:

> Moreover, as a further fruit of this uncertainty there has been an
> attempt to construct yet another special study which demands
> mention.  This is the so-called *Automatics*, the study of the
> realization in mechanism of motions either supposed or given by
> mathematical expressions.  For this further attempt at separation we
> have to thank the engineer E. Stamm, who wishes again to divide his
> subject into pure and applied parts, as fully described in his
> _Essai sur l’automatique pure_, 1863.

Of course the term “automaton” is much older.

When Reuleaux speaks of “the real end before us — the progressive
development of the machine,” it is difficult to avoid being reminded
of Forster’s _The Machine Stops_ from a few years later.

The modern development of flexure design methods is a disquieting echo
of the disjointed and “wrecked” development of mechanisms of which
Reuleaux complains; the stamp-collecting botany of the _Handbook of
Compliant Mechanisms_ closely resembles what he deplores in Monge’s
and Lanz’s work.  This is startling since, of course, we have a fairly
complete theory of elastic deformation as well as well-developed
practical numerical methods of computation.  But in Reuleaux’s time,
the analogous theory of rigid motion was similarly well-developed; you
can design a planar four-bar linkage to tween between three
predetermined positions with compass and straightedge, and the whole
lovely theory of quaternions commonly used for rigid motion in modern
games engines predated this book by decades.  As Reuleaux laments:

> Here again is a point from which the weakness of the method hitherto
> employed can be surveyed at a glance.  Its difference from the ideal
> method is not that it employs the inductive instead of the deductive
> method ; that would indeed be no advantage, but it might still be
> defensible.  No, it has been entirely unmethodical.  It has chosen
> no fixed method of investigation, or rather, it has not found any in
> spite of zealous search...

Rhetorically, this Introduction is a masterpiece; Reuleaux promises
the world to his diligent student, while warning them of the
difficulty of their quest and deprecating their existing knowledge:

> We often do not know ourselves how closely wedged-in our ideas are
> by the boundaries which education and study have drawn around
> us. ... all these pile up mighty hindrances.  I cannot therefore
> shorten the way, although the truths to which it leads are of great
> simplicity. ... to conclude in the words of Göthe, ” What is not
> understood is not possessed.”

It’s interesting to note that in Reuleaux’s time it was not yet known
how recent the introduction of the wheel was, due to the still
primitive state of the archaeological science; he claims “carriages”
are known from the oldest times, while in fact they seem to postdate
even such recent artifacts as the Great Pyramid.

His account here of Watt’s parallel motion is sticking with me.  I
keep thinking about it.

General Outlines
----------------

### Nature of the Machine-Problem ###

I just can’t get over the evocative nature of the prose, and the sharp
contrast with the uniform oatmeal mediocrity of modern academic
writing:

> So soon as the force *Q* begins to act it calls forth in the
> interior of the wheel, the shaft and the supports, internal
> molecular forces, opposite in direction and exactly equal to
> it. ... there are opposed to all external forces others concealed in
> the interior of the bodies forming the system,...

after which he proceeds to quote a verse from Schiller (!).

Reuleaux actually comments a bit on the question of flexures here: “In
actual machines...we use however only those materials which...alter
their form under the action of external forces very little, so little
that the corresponding variations from the original form may be
neglected.”

I enjoy his description of the strength or rigidity of bodies as being
“latent forces”, in contrast to actually existing “sensible forces”.

His definition of a machine is thought-provoking, as much for the
aspects it omits (material handling, information, control, energy,
force, thermodynamics, friction, efficiency, metrology, electricity,
programmability, strength, tolerances, troubleshooting, wear,
reliability, cost, clamping) as for what it includes:

> *A machine is a combination of resistant bodies so arranged that by
> their means the mechanical forces of nature can be compelled to do
> work accompanied by certain determinate motions.*

It’s a damned sight better than “a combination of simple machines”,
though, and it doesn’t exclude the use of kinetic energy or
hydraulics as many inferior definitions have.

It’s interesting that this more or less explicitly excludes the
boilers and condensers of the steam-engines Reuleaux has given such
prominent placement earlier.

### The Science of Machines ###

Here we see again the primacy Reuleaux grants to position and motion,
but also what a high priority he places on forces.  So far there’s no
mention of compliance and backlash except as an enemy; nothing about
springs or preloading.

### General Solution of the Machine-Problem ###

Even with the limited definition Reuleaux has given, this section
heading seems amazingly ambitious.

> The moving bodies are prevented, by bodies *in contact with them*
> [emphasis in original]... this contact ... must take place
> continually, ...

This seems to exclude backlash and clearances entirely!

I have no idea what a “plummer block” is.

This is the section where he introduces the **kinematic pair**.

It’s fascinating that in is Figure 4, the first kinematic pair he
introduces (before he even introduces the term) is not any of the
basic ones, but rather a block sliding in an irregularly curved plane
channel as the channel constrains it to rotate unevenly.  Then on the
*next* page his figures 5 and 6 are classic screw and prismatic joint.

Aha, and after Figure 9 he explains the meaning of “higher kinematic
pair”:

> Accordingly the reciprocal combinations of two elements gives us
> again *a pair of elements, which may differ from either of the
> single pairs of which it is composed.*

(And, as with his Figure 4 example of the weird sliding block, his
first explicit example of a higher kinematic pair is an arbitrary
weird thing, rather than something well-known.)

I really appreciate that Reuleaux is giving one or two examples first
and only then giving definitions.

Also, he defines *kinematic chain* here, far more clearly than I’d
heard it defined before.  So I’m finding this reading very rewarding
so far.

I’d never thought of this before, but there’s an interesting analogy
between how changing the position of one element in a closed kinematic
chain (a term I’m not sure I understand completely yet) alters the
position of all the others, and how changing the current or voltage of
one element such as a capacitor or inductor in an electrical circuit
alters the current and voltage of all the others.  In both cases you
can’t analyze a single component in isolation; to see the system
non-wholistically you must find other elements into which to decompose
it, such as eigenstates or linearly superimposed circuits.  But I
don’t know what those components could be for either a closed
kinematic chain or a nonlinear electrical circuit.  (And I don’t think
anything similar to the linear decomposition of circuits is known for
kinematic chains, or things like the linkage-synthesis papers I’ve
been reading would be using it.)

The aside “a cylindrical pin fitting a corresponding eye, the axes of
all being parallel” immediately calls to mind Hoberman spheres and the
like; if the axes instead all intersect at a point, you get the same
sort of linkage, but thus constrained to a sphere rather than a plane.
And of course there are other relationships that can provide more
interesting movements.  (Hoberman’s insight was similar but had to do
with lines drawn transversely through the centers of multiple such
joints.)

Aha, and here we have another key definition of Reuleauxian
kinematics:

> *A closed kinematic chain, of which one link is thus made
> stationary, is called a mechanism.*

At this point it’s worth comparing 19th-century European automata with
19th-century Japanese automata; while both treat the outward form and
appearance of the mechanism as being as important as its position,
work, and movement, the Japanese automata extensively used cable
drive, including on uneven cams, while the European automata like
Jaquet-Droz’s Writer used exclusively rigid bodies.  Reuleaux is
seeking to embrace not only pulleys and belts in his analysis but even
hydraulic machines, but he has barely mentioned anything flexible or
hydraulic yet, despite the world-shaking importance of the
mechanization of textile manufacture in the late 18th and early 19th
century, in part by Vaucanson himself.

So we can see in some sense why Babbage’s work was so unsuccessful and
why the Writer would not be equaled for some 150 years, roughly until
Bush’s Differential Analyzer.  To keep the world of machinery
intellectually manageable, its paradigm deliberately excluded
consideration of the aspects crucial to such achievements.  In
Reuleaux so far I see not even the smallest hint of the approach that
animated Zuse, Shannon, and Turing.

Here we see boldface in the text for a definition:

> *The effort thus applied performs mechanical work which is
> accompanied by determinate motions; the whole, that is to say, is a*
> __Machine__.

We also see the first mention of clocks, though nothing about the
questions of metrology, calibration, and cancelation of errors that
had enabled the conquest of Latitude with machinery a century before,
as well as the first mention of balances (in the sense of a
weighing-scale); though he does promise to “consider these questions
systematically” later.

One of the words whose usage has apparently changed significantly
since this book was translated is “empirically”, which seems here to
mean “by trial and error”.

He does finally mention “the spinning-machine” in this section, and
the sewing-machine.  The thread and fabric of the sewing-machine seems
to fit very poorly into Reuleaux’s theory — the whole machine exists
to put them into certain regular motions with respect to one another,
but their motion is neither rigid nor determinate, and substantial
parts of the machine exist purely in order to modulate the friction
and tension on them.

Phoronomic Propositions
-----------------------

“Phoronomic” is explained in the first section here; it means
something like “concerning the study of the geometric motions of rigid
bodies”.

### Preliminary remarks ###

Phoronomy, etc.

### Relative motion in a plane ###

Newtonian relativity.

Relative motion of two points is considered only in terms of their
distances, thus omitting rotation — rotational orientation is not
considered proper to a point.  Motion of a point relative to a plane
it’s moving in requires only its distances from two points, not three,
permitting a reflection ambiguity — perhaps chiral orientation is not
considered proper to a plane.

Interestingly, it seems that when he considers motion of a line
relative to another line, he *does* consider sliding motion along the
line to be motion.

### Temporary Centre; the Central Polygon ###

It’s surprising that he considers “the Phoronomics of point-systems”
to be “exhausted” by propositions in two dimensions only!

This idea of a “temporary center” of rotation, for any arbitrary
rotation, is very interesting.  It reminds me of the
compass-and-straightedge four-bar-linkage construction technique.

“Open polygons” occur frequently in computer graphics but they are
usually called something else.

I don’t understand this “reciprocal polygon” yet.  Why exactly must it
exist, and be reciprocal?

### § 7. Centroids; Cylindric Rolling ###

Oh, this makes the reciprocal polygon thing a bit clearer, though it’s
still not clear why it must exist.  Boy, this sure is a different
meaning of “centroid” than the one I’m familiar with, though not
*completely* unconnected — the centroid of a uniform shape is its
center of gravity, which is the center on which it turns when it has
only angular momentum.

All this terminology of “common, curtate, and prolate trochoids” is
new to me, and I think I’ll have to look it up.

When he says, “*All relative motions of con-plane figures may be
considered to be rolling motions*”, he seems to be omitting the
possibility of pure translational motion, which is the limit of
rolling about an infinitely distant center.

It’s nice that he is getting into three-dimensional objects moving
now, but I can’t help but wonder if there are possibilities of
three-dimensional motion other than the possibilities arising from
extending two-dimensional motions prismatically, although Reuleaux
claims there aren’t.  For example, motions where the instantaneous
axis of rotation twists from one moment to the next.  And what is the
instantaneous axis of rotation of a common screw?

It is now apparent that the reciprocal polygons of which Reuleaux was
speaking may have quite different shapes.

### § 8. The Determination of Centroids ###

The construction he gives for finding the second point *M*₁ in Figure
19 (p. 66) escapes me at the moment.  I will have to come back to
this.

**Oh**.  Because *M*₁ is notionally part of the same rigid body as *P*
*and Q*, its distances to them are not changed by rolling; at the
*given point in the movement has been brough into coincidence with
**O*₁, so those distances are the distances from the moved positions
**P*₁ and Q*₁.

Figure 20 in the Google scan (p. 67, 88/651) is partly illegible; it
is identical to Figure 10.  In the Archive’s scan it is perfectly
legible.  Incidentally, I think this page shows that the scans were
taken from two separate physical copies, as the Archive’s scan is
stamped, “Reese Library of the University of California”.

Aha, and here he confronts the question of pure translational motion,
using the device of “infinitely distant points”, which seems pretty
kosher, although not all this geometry so far is projectively
invariant.  (Or is it?)

Figure 22 is blowing my tiny fucking mind.

### § 9. Reduction of Centroids ###

I am totally not understanding this.  I thought the motion uniquely
determined the two centroid curves?  But now we can invent more
centroids for the same motion?

Here he confronts the question of parallel motion in all of its
complexity.

Hmm, there’s a clue about these “secondary centroids” in the part
where he talks about gear teeth (though he never says “gear”, always
“spur-wheel”).

### § 10. Rotation about a Point ###

Now Reuleaux seems to be taking up the question that had worried me
earlier, that of motions in space that are not merely the extrusion of
motions in a plane.

### § 11. Conic Rolling ###

Whoa, trippy, dude.

### § 12. Most general Form of the Relative Motion of Rigid Bodies ###

Okay, great, now we’re getting into screwing motions?

Oh wow.

### § 13. Twisting and Rolling of Ruled Surfaces ###

Holy shit.

Hmm, he has a hypoid drive here, although he doesn’t call it that.

Pairs of Elements
-----------------

### § 14. Different Forms of Pairs of Elements ###
