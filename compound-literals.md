ISO C99, and GCC since earlier, support [so-called compound
literals][0].  I've written a number of C functions that look more or
less like this:

    static struct point point_at(int x, int y)
    {
        struct point p = {.x = x, .y = y};
        return p;
    }

[0]: https://gcc.gnu.org/onlinedocs/gcc/Compound-Literals.html

The compound literal syntax allows you to write this more simply:

    static struct point point_at(int x, int y)
    {
        return (struct point){.x = x, .y = y};
    }

However, in simple cases like this, it often eliminates the need for
the function altogether, if it existed only for brevity; you can just
say `typedef struct point point; ... mp = (point){3, 4};` rather than
`mp = point_at(3, 4);`.

This is of course even more useful for arguments of functions;
consider:

    static point delta(point a, point b)
    {
        return (point) {.x = a.x - b.x, .y = a.y - b.y};
    }

    static int distsq(point a, point b)
    {
        point d = delta(a, b);
        return d.x*d.x + d.y*d.y;
    }

    ...
    printf("%d\n", distsq((point){2, -1}, (point){5, 3}));

It's a shame that C doesn't have top-down type inference for this
context, so we can't write `distsq({2, -1}, {5, 3})` and have the
compiler infer the `point` type.  Even OCaml fails us here — because
its type inference is bottom-up, to infer types in such cases it
requires the field names of record types to be unique, like 1970s C,
rather than scoping them within a single record type: [as SoftTimur
pointed out on Stack Overflow, this is an error in OCaml][1]:

>    type name =
>        { r0: int; r1: int; c0: int; c1: int;
>          typ: dtype;
>          uid: uid (* key *) }
>
>    and func =
>        { name: string;
>          typ: dtype;
>          params: var list;
>          body: block }

[1]: https://stackoverflow.com/questions/8928970/two-fields-of-two-records-have-same-label-in-ocaml

This is [in the OCaml FAQ][2].

[2]: https://caml.inria.fr/pub/old_caml_site/FAQ/FAQ_EXPERT-eng.html#labels_surcharge

(There's an interesting niche open for a language that uses structural
subtyping like OCaml's object types and polymorphic variants, but for
records with an open set of field names — the kind of thing people do
in JS or Lua or with JSON, but with static type checking.  I think
OCaml didn't have subtyping at all at the time records were added, and
its use is still controversial.)

Getting back to C, one of the more interesting uses for [so-called
designated initializers for struct fields][3] (`.x = `...) is
*optional values*.  If you have a struct initializer with any
initialized fields in it, then *all* the fields of the struct are
initialized — even if its storage class is `auto`, the unspecified
ones are initialized to 0, as in Java or Golang!  So regardless of how
many fields are in a `struct foo` you can initialize them *all* to 0
by saying something like:

[3]: https://gcc.gnu.org/onlinedocs/gcc/Designated-Inits.html#Designated-Inits

    struct foo x = {0};

(I think this may be illegal in some versions of C if the first member
of `struct foo` is some kind of aggregate, and I think it applies to
arrays as well, and I think the requirement to have at least one
initialized field has been removed in recent versions of C so `struct
foo x = {};` works too, but I'm not sure of any of those.)

So if you have a struct with a large number of fields, you can specify
that you want to initialize one or two of them:

    struct image_transforms t = { .premultiply_alpha = TRUE, .max_depth = 8 };

This kind of thing is especially useful to give named arguments to
functions with a large number of optional arguments; maybe somewhere
there's a `transform_image(&t, ...);` function you're going to invoke.
Of course, you always could have designed the interface with a bunch
of functions:

    image_transform_p t = new_image_transform();
    if (!t) return 0;
    it_set_premultiply_alpha(t, TRUE);
    it_set_max_depth(t, 8);

But this has several drawbacks compared to the designated-initializer
approach.  It's more code.  It introduces runtime failure into what
could have been statically allocated memory, or
statically-space-verified stack-allocated memory.  It takes time at
runtime to execute the function calls, although initializing
stack-allocated memory also takes time at runtime.  For
statically-allocated objects, you need to somehow run the
initialization code at startup, before anything uses the object.  It
can't be used inside an expression — it forces the caller into an
imperative style.  And the implementor of the calling interface must
write or macro-expand a large number of setter functions.

With compound literals with designated initializers, you get a sort of
verbose named-argument syntax:

    transform_image(&(struct image_transforms){
        .premultiply_alpha = TRUE, .max_depth = 8});

This is not a terribly efficient way to get named arguments, though;
since this struct has automatic storage duration, if you have 48
fields in the struct, the compiler has to emit code to initialize the
other 46, too.

The astonishing thing, though, is that in C, all of these compound
literals with automatic storage duration last to the end of their
enclosing scope, while in C++ they're treated as temporaries and
disappear rather quickly.  This means you can build up arbitrarily
complex nested structures this way, like Lisp.  Consider this
expression of the S combinator in the λ calculus:

    typedef struct ulc
    {
        const char *var;
        struct ulc *rator, *rand, *body;
    } ulc;

    ...
    ulc s = { "x", .body = &(ulc) {
            "y", .body = &(ulc) {
                "z", .body = &(ulc) {
                    .rator = &(ulc) {
                        .rator = &(ulc) { "x" }, .rand = &(ulc) { "z" }},
                    .rand = &(ulc) {
                        .rator = &(ulc) { "y" }, .rand = &(ulc) { "z" }}}}}};

Here a λ-abstraction is represented by an `ulc` having a non-null
`body` and a non-null `var`, a variable is represented by having a
null `body` and a non-null `var`, and an application of an operator to
an operand is represented by having a null `var`.

This represents some kind of argument about the merits of these
language features but I am not sure whether it is in favor or in
opposition.
