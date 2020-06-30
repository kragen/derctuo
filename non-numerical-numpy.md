I saw someone saying they'd never needed to use Numpy, and so never
learned it, because it was for a specific use case that wasn't theirs.
This seemed to me like maybe they didn't appreciate its versatility,
so I thought I'd try out some non-numerical or semi-numerical
computation with Numpy, and maybe Pandas.

Counting words in a string
--------------------------

The usual approach for counting words in Python, of course, is
`len(s.split())`.  But we can do things with Numpy too.  First, let's
get some text into a Numpy array:

    >>> import numpy as np
    >>> text = "This isn't anything more than a text string, with some words.  Let's count them all"
    >>> ta = np.array(list(' ' + text))

Now let's find the spaces and the non-space things following them:

    >>> sp = (ta == ' ')
    >>> ta[1:][sp[:-1] & ~sp[1:]]
    array(['T', 'i', 'a', 'm', 't', 'a', 't', 's', 'w', 's', 'w', 'L', 'c',
           't', 'a'], 
          dtype='|S1')
    >>> ''.join(ta[1:][sp[:-1] & ~sp[1:]])
    'TiamtatswswLcta'

That seems to have worked; we can count the words just by summing the
boolean vector:

    >>> (sp[:-1] & ~sp[1:]).sum()
    15

Let's use this approach to count the words in the King James Bible:

    >>> b = np.memmap('bible-pg10.txt', '|S1', 'r')
    >>> sp = (b == ' ')
    >>> (sp[:-1] & ~sp[1:]).sum()
    749219
    >>> len(open('bible-pg10.txt').read().split())
    824146

Hmm, what happened?

    $ wc bible-pg10.txt
     100222  824146 4452069 bible-pg10.txt

So wc agrees with .split().

    $ grep -P '^\S' bible-pg10.txt | wc
      74927  823934 4400033

So it seems like there are 74927 lines beginning with a non-whitespace
character, which precisely accounts for the difference.  We want to
treat newlines as whitespace as well, and also, if the file starts
with a word (which it does), we want to count that word too.

Current Numpy contains an `isin` function to test set membership, but
my old version doesn't.  No matter!  We can use .any() as a
substitute:

    >>> sp = (b == [[' '], ['\n'], ['\r']]).any(axis=0)
    >>> b[sp[:100]]
    memmap([' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '\r', '\n', '\r', '\n', ' ',
           ' ', ' ', ' ', ' ', ' ', ' ', ' '], 
          dtype='|S1')
    >>> ''.join(b[:100])
    '\xef\xbb\xbfThe Project Gutenberg EBook of The King James Bible\r\n\r\nThis eBook is for the use of anyone anywhe'
    >>> ws = (sp[:-1] & ~sp[1:])
    >>> ws[0] = True
    >>> b[ws[:100]]
    memmap(['\xef', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '\n', ' ', ' ', ' ',
           ' ', ' ', ' ', ' ', ' '], 
          dtype='|S1')
    >>> ''.join(b[1:][ws[:100]])
    '\xbbPGEoTKJBTeiftuoaa'

That seems pretty reasonable.