In electro-etching, an electrolyte selectively removes metal from a
metal surface by anodic dissolution; typically a vinyl mask is applied
to the surface to shield some areas, although of course a conventional
photolithographic resist like SU-8 epoxy or PMMA can also be used, and
painted-on coatings such as Sharpie are also frequently used.  This
can produce deep etching fairly quickly with high current.

If the surface is first uniformly electroplated (or otherwise coated)
with a differently reactive metal, even very shallow electro-etching
ought to be able to produce dramatic visual effects by selectively
removing the plating, followed by a subsequent treatment such as acid
etching, etching with alum, bluing, toning with sulfur, or possibly
even autocatalytic "electroless" plating.  This ought to enable
strongly nonlinear threshold effects as well: where the plating is
completely removed, the reactive surface is the substrate, and where
it is not completely removed, the reactive surface is the plating.

In cases where the underlying substrate is more reactive than the
plating, it ought to be possible to use further uniform
electro-etching at a carefully controlled voltage in between the
(modified, not standard) electrode potentials of the two materials to
selectively remove the substrate material where it is exposed, thus
deepening the initially patterned etch.

This is all prequel to suggesting that electro-etching or
electroplating with a foam of soap bubbles, as from dishwashing
liquid, should make a *freaking awesome* pattern.  The bubbles would
play the role of the vinyl resist.  Thanks to sbp for the idea.

A variant of this commonly happens in a variety of electrolytic
processes (anodization, electro-etching, electroplating,
electroforming, batteries, and so on) where the bubbles form from
electrolysis of the liquid; generally this is considered a nuisance,
since the bubbles spawn at unpredictable places, and in batteries
"depolarizers" like manganese dioxide are used to counter it.  But it
might also provide an interesting artistic texture.

In addition to soap bubbles, there are several other
surface-patterning approaches that come to mind.

Stamping patterns onto the surface of metal with a conductive rubber
stamp (graphite-filled or copper-filled, say) and electrolyte "ink" is
another possible form of electrolytic rapid patterning of metal
surfaces.

Earlier I'd suggested selective electro-etching or electrodeposition
with one or many moving electrodes very close to a metal workpiece as
a way to produce precise surface contours, or similarly electrolytic
anodization as a way to precisely produce colors.  The above-suggested
methods of "developing" an extremely thin initial etch or plating with
nonlinear effects should enable this process to pattern a surface
orders of magnitude faster, either by selectively etching away part of
a surface coating, by selectively depositing plating, or both.

Another possible way to selectively electroplate a surface is with
localized laser heating; for example, in a standard acid blue vitriol
electroplating solution, even a [5-watt blue laser has been reported
to produce this effect][0] by locally heating the solution and thus
slightly shifting the electrode potentials.

[0]: https://www.youtube.com/watch?v=w3jV58_Vv24 "02020-11-09 video by 'Breaking Taps' citing Al‐Sufi, A. K., et al. 'Laser induced copper plating.' Journal of Applied Physics 54.6 (1983): 3629-3631."

The more common way to modify a metal surface with a laser is of
course to heat it up in the air, which, depending on the degree of
heating, can oxidize it, explode tiny holes in it that expose fresh
metal, or both.  The oxide layer may also be usable as a selective
resist.  If the laser heating is carried out in a reducing atmosphere
such as hydrogen, carbon monoxide, acetylene, or vitriolic air, it
could simply remove the oxide, exposing raw metal, rather than
depositing it.

By using selective corona or other glow discharge, for example from
carbon fibers, platinum electrodes, or sharp aluminum wires, rather
than a laser, we could gain a number of other advantages.  We could
easily pattern the surface at scales well below the wavelength of
light, limited only by the diffusion of the plasma, which in turn is
largely limited by the precision with which we can control the
distance from the tooltip to the substrate.  If we are reducing a
surface oxide coating, we can use much smaller amounts of reducing
gases (or dielectric liquids), and using above-atmospheric or
below-atmospheric pressures may be more practical than they would be
with a laser.  By giving the workpiece a negative charge, we can
encourage anions from the plasma to smash into it, reducing lateral
plasma diffusion, and the anions can be more reactive than non-ionized
molecules would be.  (Butane gas, for example, is fairly inert, but a
butane plasma will contain all kinds of hydrocarbon free radicals.)
This will also tend to vaporize the tooltip electrode faster than a
glow discharge would; the electrode can contribute other helpful
materials to the mix, including in particular metals for vapor
deposition.

These processes, too, can sharpen the boundaries between surface
regions using the same kind of differential deposition-then-removal
process described earlier for electrolytic processing; for example,
first reduce the surface oxide coating everywhere, then selectively
deposit it in some places, then selectively remove it in others to
steepen its boundaries, and then apply some other reaction, specific
to either the oxide or the underlying metal, to use the pattern thus
deposited.  As another example, you could selectively deposit aluminum
in an argon atmosphere by plasma-vaporizing it, then use an oxidizing
atmosphere to selectively oxidize areas where you don't want the
aluminum.

Using a cold plasma pencil instead of just a glow discharge may permit
more flexibility, for example by allowing a higher degree of
ionization than a glow discharge can achieve, or allowing short-lived
ionized species to decay.  But it probably can't achieve as fine
precision.

Another way to pattern a surface by local heating is by resistance
heating, like a spot welder does.  At short distances you can invoke
field electron emission (20–40 V/μm, lower with a low-work-function
coating) or thermionic emission to liberate electrodes from your
"write head" with which to bombard the surface.  (At short distances
at atmospheric pressures there isn't enough gas to sustain an
avalanche discharge.)  This is actually the same process described
above for generating plasma, but with a different purpose, of heating
the surface rather than generating ions, so the current is in the
opposite direction.  By pulsing the discharge, greater peak
temperatures can be achieved at a given average power, changing the
attainable reaction products.  This heating can provoke many of the
same kinds of reactions as described above.  Also, despite what I said
above, this current direction is probably better for sputtering atoms
off the tooltip electrode.

For thus sputtering metal onto a non-conductive substrate you might
want to use two separate electrodes.  I suspect such sputtering at
atmospheric pressure should be feasible at very small scales.

Local heating and reaction is most precisely attainable with focused
electron beams or focused ion beams, but these of course require hard
vacuum and thus cannot be used to provoke reactions with gases or
volatile liquids, nor reactions that produce much of them.  Many
semiconductor photoresists are routinely patterned in this way.

Semiconductor etching processes offer further possibilities for
amplifying surface patterning, including not only the acid etching
mentioned above but also mass anisotropic etching with reactive ion
plasmas which react selectively with the exposed substrate.

If you have patterned a metal surface in such a way, you could etch
away the substrate metal underneath it — for example, etching steel
with alum, or aluminum with lye — to get a very thin foil of the
deposited pattern.  I understand that Drexler prototyped a solar-sail
material in a way similar to this, but you could also use the
resulting perforated metal foil as a photolithography mask.  A
three-layer technique may be the best solution here: first a massive,
rigid, etchable substrate; then a uniform thin foil of microns up to
hundreds of microns, which is also etchable, but resists at least one
etchant that attacks the substrate; then a "resist" mask, perhaps of
metal or metal oxide, deposited on top and patterned with submicron
thickness.  Once the "resist" is patterned, you etch the foil away
where it is exposed by the resist; once the foil has been etched all
the way through, you switch etchants and etch away the substrate while
leaving the foil unharmed.

