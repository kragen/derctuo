Can you use linear optimization to efficiently solve the OCR problem?

One reasonable linear objective function might be a weighted sum of
the entropy of the text and the number of wrong ("noise") pixels, or
perhaps the total absolute pixel error.  You can use the standard
one-of-N mixed integer linear programming approach to select which
glyph is at a given position, and a reasonable entropy function might
use digraph frequencies in your language of choice.  You might be able
to use some design variables that indicate the X-Y position of each
glyph, and the font would perhaps be a shitload of parameters.

Alternatively, simple hill-climbing with a nonlinear problem state
ment might be simpler to formulate and work adequately; it might
"learn" the font simultaneously with the position and angle of text on
the page.  Gradient descent could more efficiently provide fine glyph
positioning and adjust the glyph contents.  (Or just simple
mean/median?)

