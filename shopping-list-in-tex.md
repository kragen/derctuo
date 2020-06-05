I was watching Luke Smith on YouTube touting R-Markdown as a better
alternative to LaTeX, and I was struck by his declaration,

> Now, the thing about LaTeX, and it's always the elephant in the room
  when you're talking about LaTeX, is that a lot of the *basics*, it,
  well, let's put it this way, LaTeX is great for making *research
  papers* and *term papers* and doin' advanced projects 'n' stuff like
  that, but LaTeX syntax is *very cumbersome*.  So if I just wanna
  make a *shopping list* in LaTeX or something, I mean, I wouldn't
  make a shop, I make shopping lists on this [holding up a pad of
  paper], but, if I wanted to make a really simple document to give to
  my students or uh, you know, to give, you know, just a *memorandum*
  or something like that, uh, LaTeX is a pain because you can't just
  open it up and start writing, you have to `\documentclass{article}
  \begin{document} \end{document}`, all this kind of stuff, uh, to do
  thangs like bold, italics, you have to literally *go in and write*
  `/textbf{bla bla bla}`, and you know the *backslash*, it's like the
  most *annoying key* on the computer to actually, like, hit.

I thought I'd check to see if he was right, so I ran `emacs
shoppinglist.tex` and typed

    C-c C-e <return> <return> <return> C-c C-e <return>
    c a r n e C-c C-j h u e v o s C-c C-j p o l l o C-c
    C-b <return> <return> C-c C-b <return> C-c C-b <return>
    <return>

which produced this shopping list, rendered and on the screen in an
xdvi window:

    \documentclass{article}

    \begin{document}
    \begin{itemize}
    \item carne
    \item huevos
    \item pollo

    \end{itemize}
    \end{document}

It took about 30 seconds, but 10 of those were starting up a new Emacs
so that I could time the process more easily.

The initial ^C^E prompted me for the environment name (default
`document`) and documentclass (default `article`) and options (default
none).  The second ^C^E prompted me for another environment name; as
it happened the default was `itemize` because the last thing I'd done
in LaTeX was also to make a list, so I just hit `<return>` again.  The
^C^J is the sequence to separate list items.  Then the ^C^B sequences
run latex and xdvi to see the rendered document.

Unsurprisingly he's also wrong about "things like bold, italics";
although you can `\textbf` if you want, it's probably easier to say

    PUAs are {\bf losers}.

Which renders as, "PUAs are **losers**."  It's two characters longer
than the Markdown version, admittedly, and I do like Markdown a lot,
but I think LaTeX is getting a bad rap here.  You can totally write
your shopping lists in LaTeX --- it's not quite as easy as writing
them in Markdown but the difference is very small.  Maybe 10 seconds
of overhead.

Where LaTeX becomes difficult is when you're trying to do more complex
things in it.  Markdown saves you time there because you know you
can't do complex things at all in Markdown, so you don't try.

The major advantage of R-Markdown from my point of view is that you
can embed your R code in it.
