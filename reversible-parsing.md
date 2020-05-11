In Prolog you can write definite clause grammars, which make it very
straightforward to write grammars, which can then be used both for
text generation and for parsing:

    : user@debian:~/devel/dev3; swipl
    % library(swi_hooks) compiled into pce_swi_hooks 0.00 sec, 3,856 bytes
    Welcome to SWI-Prolog (Multi-threaded, 64 bits, Version 5.10.4)
    Copyright (c) 1990-2011 University of Amsterdam, VU Amsterdam
    SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software,
    and you are welcome to redistribute it under certain conditions.
    Please visit http://www.swi-prolog.org for details.

    For help, use ?- help(Topic). or ?- apropos(Word).

    ?- [user].
    det --> [the] | [a] | [that].
    |: noun --> [buffalo] | [capacitor] | [philosophy].
    |: vi --> [sucks] | [is, walking] | [glows].
    |: vt --> [supersedes] | [clobbers] | [loves].
    |: sentence --> det, noun, vi |
    |:  det, noun, vt, det, noun.
    |: 
    % user://1 compiled 0.00 sec, 4,816 bytes
    true.

    ?- phrase(sentence, S), append(Y, Z, S), append(X, [buffalo], Y).
    S = [the, buffalo, sucks],
    Y = [the, buffalo],
    Z = [sucks],
    X = [the] ;
    S = [the, buffalo, is, walking],
    Y = [the, buffalo],
    Z = [is, walking],
    X = [the] ;
    S = [the, buffalo, glows],
    Y = [the, buffalo],
    Z = [glows],
    X = [the] ;
    S = [a, buffalo, sucks],
    Y = [a, buffalo],
    Z = [sucks],
    X = [a] ;
    S = [a, buffalo, is, walking],
    Y = [a, buffalo],
    Z = [is, walking],
    X = [a] ;
    ...
    S = [the, buffalo, loves, that, capacitor],
    Y = [the, buffalo],
    Z = [loves, that, capacitor],
    X = [the] ;
    S = [the, buffalo, loves, that, philosophy],
    Y = [the, buffalo],
    Z = [loves, that, philosophy],
    X = [the] ;
    S = Y, Y = [the, capacitor, supersedes, the, buffalo],
    Z = [],
    X = [the, capacitor, supersedes, the] ;
    S = Y, Y = [the, capacitor, supersedes, a, buffalo],
    Z = [],
    X = [the, capacitor, supersedes, a] .

    ?- 

A disadvantage of DCGs is that, in standard Prolog, they don’t
terminate on left recursion and can take exponential time, although
cuts can tame the exponential and I think tabled resolution can
conquer both in some cases (“[DCGs + Memoing = Packrat Parsing, But is
it worth it?][0]” by Ralph Becket and Zoltan Somogyi.)

[0]: https://mercurylang.org/documentation/papers/packrat.pdf

Hmm, clearly I have a lot to learn about Prolog DCGs... [Markus
Triska’s tutorial][1], [Anne Ogborn’s tutorial][2], [the SWI-Prolog
manual][3], and so on.

[1]: https://www.metalevel.at/prolog/dcg
[2]: http://www.pathwayslms.com/swipltuts/dcg/
[3]: https://www.swi-prolog.org/pldoc/man?section=DCG

Anyway, what I was thinking was that for very straightforward kinds of
“grammars”, even a perfectly ordinary imperative language suffices:

    void employee_card(card *s, employee *e)
    {
      int_columns(s, 0, 6, &e->empno);
      columns(s, 6, 16, e->firstname, sizeof e->firstname);
      columns(s, 16, 26, e->lastname, sizeof e->lastname);
      blank_columns(s, 26, 80);
    }

This plain C function could be invoked either for input or for output,
if `card` contains a flag that indicates the direction and the
`int_columns` and `columns` functions consult that flag.  And similar
bidirectional serialization/deserialization functions can be built for
a wider class of grammars.  Field widths need not be fixed, and field
concatenation can be implicit:

    void employee_csv(stream *s, employee *e)
    {
      int_field(s, &e->empno);
      text(s, ",");
      delim_s_field(s, e->firstname, sizeof e->firstname, ',');
      delim_s_field(s, e->lastname, sizeof e->lastname, '\n');
    }

Again, this function can be used either for input or for output, and
multiple such functions can be composed together.  If we add a little
bit of backtracking, we can get polymorphic records:

    void foo(stream *s, thing *t)
    {
      begin(s);
      {
        equal_int(s, &t->type, TYPE_BAR);
        byte(s, 'B');
        nbytes(s, &t->bar.contents, sizeof t->bar.contents);
      }
      or(s);
      {
        equal_int(s, &t->type, TYPE_QUUX);
        byte(s, 'Q');
        s16_le(s, &t->quux.len);
        nbytes(s, &t->quux.contents, t->quux.len);
      }
      end(s);
    }

On input, the calls to `equal_int` function as assignments to an
integer field, while the calls to `byte` function as assertions about
which byte comes next in the input; if one of these assertions fails,
its effect on the input stream is backtracked, so that a subsequent
call to `byte` can test the same input bytes.  The backtracking state
is set up by `begin`, restored by `or` in case of failure, and torn
down by `end`.

On output, the situation is precisely the other way around: the calls
to `equal_int` function as assertions about what should be found in
`t->type` for that branch to proceed successfully, while the calls to
`byte` emit literal bytes on the output — bytes which are buffered so
they can be retracted if the case must be backtracked due to a
subsequent failed assertion.

But this is still a very simple case; in particular it does not handle
allocation, which in a C-like language probably must be part of the
state restored in case of backtracking.

You could consider something like

    child_node(s, &t->child, sizeof struct fulano);
    struct fulano *f = (struct fulano *)t->child;
    equal_int(s, &f->type, TYPE_FULANO);
    byte(s, 'f');
    decimal_int(s, &f->x);
    byte(s, ' ');
    decimal_int(s, &f->y);

where `child_node` creates a new allocation on input (deallocated in
case of backtracking) and does nothing on output.  But consider the
infix expression

    3 + 1000/2/2/2/2/2

which in prefix notation is

    (+ 3 (/ (/ (/ (/ (/ 1000 2) 2) 2) 2) 2))

so unfortunately we have to read the rest of the input before we know
how deep down the tree 1000 goes.  I’m not even sure Prolog DCGs
handle this case in this form.  I wrote a toy calculator program
tonight to explore some of the above ideas, and I refactored the
grammar to eliminate left recursion; here’s a simplified form of how
it parses terms like `1000/2/2`:

    int term()
    {
      int x = unary();

      begin();
      while (ok()) {
        /* save() ensures that our progress so far will not be backtracked */
        save();      /* zero or more multipliers, divisors, and modulos */
        begin();
        {
          op("*");
          int y = unary();
          if (ok()) x *= y;
        }
        or();
        {
          op("/");
          int y = unary();
          if (ok()) x /= y;
        }
        end();
      }
      end();

      return x;
    }

`x /= y` in an AST would be something like `x = new_division_node(x,
y)`.  But it’s deeply unclear to me how to make that work
bidirectionally: the sequence of the input text is bottom-up, while
normally the structure of an AST is top-down.

A somewhat related thing is operator precedence and associativity.  If
we take `+` to be associative, it might be reasonable to serialize
both `(+ 1 (+ 2 3))` and `(+ (+ 1 2) 3)` in infix as `1 + 2 + 3`, but
clearly `(- 1 (- 2 3))` is `1 - (2 - 3)` while `(- (- 1 2) 3)` is
conventionally `1 - 2 - 3`.  Similarly, precedence dictates that `(+ 3
(* 4 5))` can be `3 + 4 * 5`, but `(* (+ 3 4) 5)` requires extra
parentheses: `(3 + 4) * 5`.
