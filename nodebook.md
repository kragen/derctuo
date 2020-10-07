
    05:16 <xentrac> I think I have a UI direction for bicicletaish/halpish stuff that I think might be really 
                    appealing
    05:17 <xentrac> the basic interaction is that you're typing text into a text editor, but the editor is 
                    watching your text for "quantities"
    05:18 <xentrac> it might have different patterns for a "quantity".  Like clearly 3.14 would be a quantity, and 
                    for my purposes I want to also recognize things like 3.14 mm and 3.14 m/s
    05:18 <xentrac> but you could imagine an arbitrarily wide range of quantities.  anyway it's looking for them 
                    in your text and initially it just tags them, say with a dotted underline
    05:19 <xentrac> now that gives you the option of scrubbing it with your mouse to change it, bret-victor-style, 
                    but so far that's not very useful
    05:20 <xentrac> you can correct its autotagging if it fails to notice a quantity or if its parse boundaries 
    05:21 <xentrac> so this nodebook node is, so far, just some text with markup.  and, I don't know, maybe if you 
                    type a #fe77cc color you get a color swatch, and clicking on it pops up a color picker, or 
                    something
    05:23 <xentrac> so the next thing you can do is that you can initiate a calculation, which starts by popping 
                    up a menu of recognized quantities in the neighborhood, and you can select one of them or you 
                    can start typing more numbers and operators and stuff
    05:24 <xentrac> and so you can perform a concrete calculation on these concrete quantities, and by default the 
                    formula and the result are displayed there in your text too
    05:25 <xentrac> so now when you scrub on things your document responds, recalculating.  and you can mouse over 
                    parts of the formula or navigate it with keys in order to see intermediate quantities
    05:26 <xentrac> it's still all basically text, though maybe latex or something is rendering your formulas
    05:28 <xentrac> so maybe you write "An air conditioner of 2 ton capacity is 7033 W; if it consumes 12 A at 240 
                    V, that's 2880 W, so its coefficient of performance is 2.44."
    05:28 <xentrac> and here 7033 W, 2880 W, and 2.44 are all calculation results, which might have formulas 
                    displayed before them
    05:28 <xentrac> And you probably have some kind of command to control the units and precision of such displays
    05:30 <xentrac> the next thing you can do is to name the quantities, whether directly entered quantities or 
                    the results of calculations (which are implemented in the same way for execution, but perhaps 
                    not in the user interface)
    05:32 <xentrac> now, once you have named the quantities, you can do what-if questions, like "By contrast, if 
                    it must consume {i2=} 25 A, its CoP is {this{i=this.i2}.cop:%.2f}."
    05:33 <xentrac> where the stuff in {} is not displayed in the document but is a goofy way I just came up with 
                    to try to describe what's underneath.
    05:34 <xentrac> This also allows you to do optimization ("goal-seek") calculations where you specify a model, 
                    a set of design variables, and an objective function to minimize
    05:34 <xentrac> and plotting, where you specify a range for one or more independent variables and one or more 
                    dependent variables to plot, along with specifying what kind of plotting you want
    05:36 <xentrac> and tabulation, where you specify a set of columns, the number of rows, and override values 
                    for some cells
    05:37 <xentrac> note that so far this is all without any functional abstraction!  no nested scopes yet, just 
                    "the document (or universe or node)" and conditional versions of the document with some 
                    variables overridden and maybe not fully displayed
    05:38 <xentrac> there's a hierarchical structure to the execution but not to the definition (except insofar as 
                    maybe a formula might be written in a context-free language rather than, say, Forth)
    05:40 <xentrac> now if you can write multiple independent documents like this, you maybe have everything you 
                    need, but I do think it would be handy to highlight a block and say "refactor tthis block of 
                    text into a child node"
    05:40 <xentrac> which would leave the display of the document pretty much unchanged, except for some formulas, 
                    since by default you'd be transcluding the child node there
    05:41 <xentrac> but would maybe make it easier to reuse
    05:42 <xentrac> I think this is a more appealing user interface than observablehq, but it can support the same 
                    kinds of interactions
    05:44 <xentrac> It supports fully concrete example-based computation, with conditionals and lazy evaluation 
                    it's Turing-complete, and it doesn't inherently require vectors in the data model, for better 
                    or wose
    05:44 <xentrac> worse
    05:47 <xentrac> and of course each of these "documents" or "nodes" could exist in a "nodebook", with or 
                    without a [human-readable] name, and be invocable by one another
    05:48 <xentrac> for testing purposes, I think it would be useful to have an "assertEqual" formula operator 
                    which produces an error object with explanatory text and a hyperlink to its invocation site
    05:49 <xentrac> and that interpolation of such an error object into a textual template (since the underlying 
                    representation of the node is a set of instance variable definitions, the value of one of 
                    which is a formula using a textual template and a textual substitution operator)
    05:50 <xentrac> would produce text similar to non-erroneous interpolation, but flagged as an error as well
    05:51 <xentrac> so you could just look for (top-level) nodes whose display value was an error, and put a bunch 
                    of asserts into one as tests
    05:51 <xentrac> maybe they would be red in a display
    05:51 <xentrac> I'm curious what you think, and sorry for subjecting you to this steam-of-consciousness 
                    explanation

This seems like it might really work as a Wiki thingy.