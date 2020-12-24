There’s a lot of recent HCI research on the impact of UI latency on
computer usability.  The picture is abysmal: [keyboard latency is
70–200 ms][0] and touchscreen latency is 70–250; even musical
performance systems like the [Reactable][1] suffer from 100–300 ms
input lag (I counted frames in that video); the experimental tangible
programming system at [Dynamicland][2] is kneecapped by a latency in
the 600–2500 ms range.  Meanwhile, [people can tell the difference
between 1 ms and 2 ms of dragging lag][3] in user tests with a
custom-built low-latency stylus tracking system, or [detect 10 ms of
latency in a different experiment][5]; [10 ms of latency at dragging
or drawing is extremely obvious][6]; performance in the simplest
touchscreen tasks is [hampered by input latency over 25–50 ms][4]; and
with 1–3 ms of latency, [projection-mapping can convincingly add
textures to moving and flexing objects][7], [even when some of the
light leaks around the edges][8] or [ripple in the wind][9], though
[commercially available real-time projection mapping systems let the
projection mapping visibly slide][11] around over the moving surface
due to their higher latency, thus failing to achieve the desired
illusion.

[0]: https://danluu.com/input-lag/
[1]: https://www.youtube.com/watch?v=Bg1SPdWEjuo "A Reactable performance from 2019"
[2]: https://www.youtube.com/watch?v=HPvRPOeKUx0 "Cayley diagrams"
[3]: https://webdocs.cs.ualberta.ca/~wfb/publications/C-2014-SIGCHI-Latency.pdf
[4]: https://www.youtube.com/watch?v=A2yGdqGUpdk "Ishikawa Group’s video figure on Allowable Limits of Latencies in Delay Control Visual Feedback System"
[5]: https://www.tactuallabs.com/papers/howMuchFasterIsFastEnoughCHI15.pdf
[6]: https://www.youtube.com/watch?v=vOvQCPLkPt4 "Paul Dietz at MSR talking about latency and demonstrating 1-ms latency"
[7]: https://www.youtube.com/watch?v=QDppJ9NWtaE "Ishikawa Watanabe Lab DynaFlash v2 demo video"
[8]: https://www.youtube.com/watch?v=XEseo-orRDI "Ishikawa Senoo Lab VarioLight demo video"
[9]: https://www.youtube.com/watch?v=-bh1MHuA5jU "Ishikawa Watanabe Lab video figure for 'Dynamic projection mapping onto deforming non-rigid surface'"
[11]: https://www.youtube.com/watch?v=XkXrLZmnQ_M "Panasonic real-time tracking and projection mapping marketing video"

The Microsoft system used above used a custom-built “high-performance
stylus system” using Gray-code-modulated light patterns projected at
24kHz with a [TI DLP DMD “projector development kit” which costs
US$1800][10]; the Ishikawa .\* Lab research used both a projector with
a custom-built 1000fps tracking mirror setup (using what looks like
the kind of galvo rotating mirrors used for laser shows) and a
custom-built 1000fps camera.  Lower-cost DMD devices like the [TI
DLP4710 chip][12] can only manage 120Hz [but still cost US$170][13].

[10]: https://www.ti.com/tool/DLPD4X00KIT
[12]: https://www.ti.com/product/DLP4710
[13]: https://www.digikey.com/en/products/detail/texas-instruments/DLP4710FQL/12604457

Is there nothing we can do to do HCI experimentation with low-latency
user interfaces without shelling out the big bucks?

Two possibilities occur to me: light pens and Wacom tablets.

A light pen — described in Ivan Sutherland’s SKETCHPAD dissertation,
but I forget if he invented the thing or not — is a
high-temporal-resolution light sensor in a tube.  You point it at a
CRT screen, and it detects the time when the electron beam illuminates
the point the pen is pointed at on the screen.  A [6ns photodiode
covering the whole visible spectrum will run you 26¢ in quantity 10 at
Digi-Key][14] or [63¢ for a bigger 5ns one][15], although some common
photodiodes are as slow as 100ns.  An [NTSC TV set runs 29.97 full
interlaced frames per second][16] at 525 lines per frame (486
visible), so the electron beam in an NTSC CRT takes 63.6 μs to sweep
across each line, including the [10.9 μs horizontal blanking
interval][17].  Typically about a quarter of the screen is XXX
So 100 ns is a 527th of a scan line.

[14]: https://www.digikey.com/en/products/detail/everlight-electronics-co-ltd/PD204-6C/2675631
[15]: https://www.digikey.com/en/products/detail/osram-opto-semiconductors-inc/SFH-213/607286
[16]: https://en.wikipedia.org/wiki/NTSC#Lines_and_refresh_rate
[17]: https://en.wikipedia.org/wiki/Horizontal_blanking_interval

Thanks to Brandon Moore for the discussion this note arose from.
