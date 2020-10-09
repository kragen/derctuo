LOGSL: Lisp object-graph serialization language.  Not a markup
language.  I insist that it is not merely aping YAML.

Problem to solve
----------------

The problem to solve is mostly the problem of Python pickle: to
serialize a possibly-cyclic in-memory object graph, then deserialize
it.  However, I have a couple of desires that pickle fails to fulfill:

1. I would like it to be mostly human-readable and line-oriented so
that I can check the result in to Git and successfully resolve update
conflicts.

2. I would like it to not be a security hole by default, even at the
possible cost of being less convenient to use.

(I seem to be undecided about whether this is a
single-programming-language thing or a cross-programming-language
thing.  Maybe it should be a single-programming-language thing.  And
maybe that language shouldn't be Python.)

Inspiring examples
------------------

Here are some examples of syntax I think might be worth supporting:

    - one
    - two
    - three

That's a list or array containing three byte strings.

    x 37
    y 38

That's the dictionary represented in JSON as {"x": 37, "y": 38}.  The
ordering of the keys is mandatorily ASCIIbetical.

    [Point]
    x 37
    y 38
    label A

That's an object of class Point whose instance variables are {"x": 37,
"y": 38, "label": "A"}.  The ordering of the keys is mandatorily
ASCIIbetical.

    - "31"
    - "32"

That's a list of two strings.  Without the quotes they would be
integers.  Strings that contain only ASCII alphanumeric characters and
the punctuation `_`, `-`, `.`, `?`, and `@`, and do not start with
"-", ".", or a digit, must be represented as barewords as in the
previous examples.  All other strings, such as those that start with
"3" or contain spaces, must be represented with doublequotes,
backslashing backslashes and embedded doublequotes.

    [Rect]
    start
        [Point]
        x 1.5
        y 2.4
    end
        [Point]
        x 3.1
        y 2.6

That's an object of class Rect whose instance variables start and end
are Point objects.  The indentation must be four spaces.

    - "ø"u

That's a list containing a Unicode string consisting of a single
codepoint.  In the concrete syntax this codepoint is represented by a
quote, two UTF-8 bytes, another quote, and a lowercase "u".  This
bullshit is Python's fault, and in decent languages that just store
Unicode in byte strings as Pike and Ritchie intended, producing such
an abortion will require the use of a custom mapping to a
LOGSL-specific Unicode class.

    # John Doe
    [Person]
    firstname John
    lastname Doe
    wife (Mary Roe)

That's a definition of an object labeled "John Doe" so that it can be
referred to elsewhere, specifically by the reference "(John Doe)".
Its instance variable "wife" is indirected through just such a
reference, to an object named "Mary Roe".  Such definitions must occur
in ASCIIbetical order following the main object graph.  Their
identifiers are arbitrary but must be unique.  All those objects that
are referred to more than once must be defined in this way.  Other
objects may be defined in this way as well, for example to keep
indentation manageable.

No objects not transitively referenced from the main object graph may
be thus defined.

The label line "# John Doe" must be preceded by a blank line, unless
it is at the beginning of the file.  Other blank lines are forbidden
in LOGSL.

If such a label line is at the beginning of the file, it is a label
for the root of the main object graph, enabling things within the
object graph to refer to that root.

The main object graph, and indeed all such top-level objects (the
others being labeled objects), is constrained to be an aggregate
object such as a dictionary, a list, or a class instance, not a
primitive object such as a string, a number, or null, which is
represented as "???".

Hmm, that restriction could be avoided, especially with colons:

    # John Doe
    [Person]
    firstname: John
    lastname: Doe
    wife: (Mary Roe)

Dictionary keys that are compound objects could be referred to by title:

    (John Doe) 5
    (Mary Roe) 18

Python calling interface
------------------------

To enable serialization and deserialization of class instances without
implicitly granting LOGSL sources the permission to instantiate
arbitrary classes, a set of classes or other factories must be
provided to the deserializer.  Each must be possessed of a unique
name.

In Python, the default behavior for deserialization should be
something like the following.  Get the name from `__name__`.  In the
case of classes, magically set up an object as follows:

    obj = klass.__new__(klass)
    obj.__dict__ = instance_variables

Other behaviors can be provided by a factory object that has
`__name__` and can be called; an AliasedClass factory is provided to
enable the resolution of name conflicts:

    class AliasedClass:
        def __init__(self, name, klass):
            self.__name__ = name
            self.klass = klass

        def __call__(self, instance_variables):
            obj = self.klass.__new__(self.klass)
            obj.__dict__ = instance_variables
            return obj

Other factory functions or objects can be used to support schema
upgrade.

For serialization, we need to supply more or less the same whitelist,
and also the possibility of snipping unwanted object references at
output time --- the link from the banana to the gorilla, or at least
from the gorilla to the rest of the jungle.

Python pickle does this by defining methods on the banana object; at
this point the interface ("the copy protocol") is extremely complex,
involving methods known as `__getstate__`, `__getnewargs__`,
`__getnewargs_ex__` (I'm not kidding), `__reduce__`, and, just to add
insult to injury, `__reduce_ex__`.  In a simple case,
`Banana.__getstate__` can simply return a copy of its instance
variables dictionary with `gorilla` set to null (`None`).

I think that probably a better approach for such cases is to include
something other than a class in the whitelist of "classes", which
undertakes the work of computing different serialization data.  The
simplest case is AliasedClass, where we might want to map the class
name of the object back to the alias we're expecting to find at
deserialization time.  This requires making an entry that maps the
runtime dynamic class to the AliasedClass instance.  But in another
case we might want to, say, produce a "reduced" banana:

    def Banana(banana):
        d = banana.__dict__.copy()
        del d['gorilla']
        return 'Banana', d

Somehow, this function must be associated with the class it is
intended to reduce, perhaps with a function attribute like
`Banana.klass = fruits.Banana`.

It may be worthwhile to define a similar sort of thing for producing
candidate labels like the "John Doe" example above.  Python's default
`repr` for class instances is terrible in that it includes hexadecimal
memory addresses, which create unnecessary merge conflicts and false
diffs in IPython notebooks.  Even "Point 1", "Point 2", "Point 3"
would be better, but "Point x=37" would be better still.  "Rect
start=<__main__.Point object at 0xb64858ac>" would not be an
improvement, though.

Golang calling interface
------------------------

The Golang standard library includes serialization in "Gob", JSON,
generic arbitrary XML, and a generic "binary" format.  All of these
use reflection a lot.  So I think it's probably okay for LOGSL to use
reflection too.

I don't know how to use reflection in Golang but I bet the source code
for those four standard library modules is a good example to work
from.

JS calling interface
--------------------

JS lacks byte strings.  Otherwise I think it'll be pretty similar to
Python.