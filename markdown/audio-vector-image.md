I was just thinking about transmitting a live vector image over analog
ultrasound in as simple a way as possible, for example to provide an
oscilloscope display.  The minimum is an X coordinate and a Y
coordinate, with brightness/blanking being optional; if we want the
image to stay “live”, rather than “slow-scan”, we need at least 24
fps.  If it’s analog there isn’t a defined X resolution or Y
resolution, more a signal-to-noise ratio kind of thing.  So then we
have the question of how complex a picture we want to be able to
encode.

Probably the minimum interesting picture is a single letter, which is
about two or three cycles per frame, and so at least 48 Hz of
bandwidth per channel, probably more like 100 Hz per channel.  You
could encode the signal in any number of ways: AM, suppressed-carrier
single sideband, FM, and so on.  AM uses up twice the bandwidth but is
otherwise pretty identical in characteristics.

The next step up might be a word, maybe 500 Hz per channel for five
letters, maybe 15 or 20 cycles per frame.  This is sufficient for
simple oscilloscope waveforms, too, if you modulate the X scan to slow
down before reaching sharp peaks and valleys.  Simple analog circuitry
can’t do that, though.

The next step up in complexity might be 3000 Hz per channel, and at
this point I’m going to guess that this is about 60 curved lines, or a
reasonably elaborate drawing.  At this point, if we’re trying to fit
in underneath the 20kHz cutoff of many audio systems, people with good
hearing will be able to notice the signal, because it’ll be 14 kHz to
20 kHz.