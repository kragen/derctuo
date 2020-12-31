Fractint stored the fractal parameters in its GIF89a output files, so
that if you or someone else loaded them into Fractint later, you could
recalculate at a higher resolution, or explore other parts of the
fractal, or vary parameters.

Screenshots or PNG files are a common way to share circuit diagrams
from circuit simulation programs.  What if machine-readable circuit
topology information was included in these files?  You could take the
Fractint approach and just include a special parameter block, but that
will be lost if someone, say, crops the image, or uses a screenshot
program to get the image, or transcodes it to JPEG.  What if you sort
of barcode or watermark it in the image itself?

The first circuit given in [Level Shifter] has 6 components; in the
encoding used by Falstad's circuit simulator, it occupies 224 bytes,
or 133 bytes gzipped, about 22 bytes per component.  I think it's
probably possible to beat this by about a factor of 3, about 7 bytes
per component, 56 bits per component.  Given that each component
occupies about 500 pixels it seems like it should be pretty feasible
to do this.

[Level Shifter]: level-shifter.md
