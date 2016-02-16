.. default-role:: literal
.. _hardware-model:

**************
Hardware Model
**************

At it's very core, Wiggleport is based on a way of interacting with hardware by *modeling* the way data flows. These models contain anything the computer needs to know about some hardware in order to communicate with it, including how to configure any programmable parts of that hardware. Wiggleport's modularity comes from the way these models can be freely reassembled without rewriting driver software.

Like the Document Object Model you may be familiar with from web browsers, Wiggleport has a hardware model based on a tree-structured collection of objects. Whereas the web was born from the fairly complex standard that became XML, Wiggleport starts with something simpler: JSON_.


.. _modeling-with-json:

Modeling with JSON
==================

JSON_ itself is designed to be very simple and unambiguous for software to parse, but it can be inconvenient to read and write by hand. In describing hardware models, we will often use YAML_ as an alternate syntax for these same objects.


.. _numbers:

Numbers
-------

.. highlight:: json

JSON_ supports integers like ``524287`` and ``-42``, or floating point numbers like ``10.5`` and ``3e8``.  We will refer to these all as *numbers* usually. Integers are automatically promoted to floating point numbers as necessary. Integers are signed 64-bit numbers, and floating point values use 64-bit `IEEE double`_ precision.

.. highlight:: yaml

In modeling hardware, it's often helpful to use hexadecimal_ numbers. JSON does not support hexadecimal numbers, but both YAML_ and Wiggleport's :ref:`expressions` support hex numbers like ``0x2AF0`` and ``0x100000000``.


.. _strings:

Strings
-------

Strings of Unicode_ characters can take on a variety of roles in Wiggleport's hardware models. On its own, a string has no implied format. It can represent freeform metadata like a device's name or its version. :ref:`expressions` use specially formatted strings to represent rules for values that can change.

In JSON_, all strings must be surrounded with double-quotes. Much of YAML_'s readability comes from relaxing this requirement. Quotes can often be entirely omitted in YAML_, as the parser assumes any unintelligible data must be a string value.


.. _special-values:

Special Values
--------------

.. highlight:: json

JSON_ and YAML_ also both provide the boolean_ values ``true`` and ``false``, as well as the nothingness placeholder value ``null``.

.. _boolean: https://en.wikipedia.org/wiki/Boolean_algebra

.. highlight:: yaml

JSON_ does not support comments at all, since it's designed for computers rather than humans.
YAML_ lets you document your models using comment lines beginning with ``#``.


.. _arrays:

Arrays
------

.. highlight:: yaml

JSON_ has simple arrays like ``[1, 2, 3]``. They can hold any number of things, of various types.
YAML_ supports this compact syntax, as well as a longer format that reads like a list of bullet points::

    - This is a string, beginning a longer list
    - A second string
    - [1, 2, 3]
    -
      - Pineapple
      - Kitten

.. highlight:: json

The equivalent JSON_ for this example would be::

    [
        "This is a string, beginning a longer list",
        "A second string",
        [1, 2, 3],
        ["Pineapple", "Kitten"]
    ]


.. _objects:

Objects
-------

.. highlight:: json

Objects are unordered pairs of names (strings) and values of any type. JSON_ uses a very strict subset of the ``{ "name": "value" }`` syntax that may be familiar from Javascript. YAML_ trades this explicit syntax for a more fluent interpretation based on indentation level and context:

.. code-block:: yaml

    number: 42
    greeting: Hello, people of Earth!
    array:
      - 1
      - 2
      - 3
      - banana   # Comments are okay!
    objects:
      etc:
        thing: 99
        'and more': 42
      empty: null
    boolean:
      - true
      - false

The same object could be represented in JSON_ somewhat more verbosely as::

    {
      "number": 42,
      "greeting": "Hello, people of Earth!",
      "array": [
        1,
        2,
        3,
        "banana"
      ],
      "objects": {
        "etc": {
          "thing": 99,
          "and more": 42
        },
        "empty": null
      },
      "boolean": [
        true,
        false
      ]
    }


.. _references:

References
----------

.. highlight:: yaml

In Wiggleport's use of JSON, we assume every value within an object can be uniquely identified by its name. Values within nested objects can be referenced using a dotted syntax. For example, `objects.etc.thing` could refer to the value ``99`` in the example above. For this to work, the strings `objects`, `etc`, and `thing` must all be valid :ref:`identifiers`. The ``42`` above can't be referenced this way, because `and more` is not a valid identifier.

.. productionlist::
    reference: `identifier` ("." `identifier`)*

When a reference is encountered in the model, it must be *resolved* to a specific object by searching for each identifier in turn. The starting point in this search is its *scope*, and in fact each reference typically has access to several nested scopes.

For example, in YAML_, the following references `ref1` through `ref8` are strings interpreted as references according to their location in the model. References `ref1` through `ref4` search only the root scope, whereas references `ref5` through `ref8` have three scopes to search in order: `deeper`, `inside`, then lastly the root object::

    ref1: name                # → "outer"
    ref2: inside.name         # → "middle"
    ref3: inside.deeper.name  # → "inner"
    ref4: deeper.name         # → null

    name: outer
    inside:
      name: middle
      deeper:
        name: inner

        ref5: name                # → "inner"
        ref6: inside.name         # → "middle"
        ref7: deeper.name         # → "inner"
        ref8: inside.deeper.name  # → "inner"

The consequences for an invalid reference depend on context. For example, :ref:`expressions` will not parse if any references within fail to resolve. Typically this will lead to a reported error as soon as that part of the model loads.


.. _identifiers:

Identifiers
-----------

.. highlight:: yaml

In short, identifiers are single words that don't start with a number or contain any punctuation other than the underscore (`_`) character. Identifiers never contain spaces.

For a precise definition of what an Identifier means in Unicode_, Wiggleport follows in the footsteps of languages like C++11 and Swift with a simplified definition that doesn't require hefty character trait tables:

.. productionlist::
    identifier: `id_start` `id_continue`*
    id_start: a-z | A-Z | "_" |
            : U+00A8 | U+00AA | U+00AD | U+00AF |
            : U+00B2–U+00B5 | U+00B7–U+00BA |
            : U+00BC–U+00BE | U+00C0–U+00D6 |
            : U+00D8–U+00F6 | U+00F8–U+00FF |
            : U+0100–U+02FF | U+0370–U+167F |
            : U+1681–U+180D | U+180F–U+1DBF |
            : U+1E00–U+1FFF | U+200B–U+200D |
            : U+202A–U+202E | U+203F–U+2040 | U+2054 |
            : U+2060–U+206F | U+2070–U+20CF |
            : U+2100–U+218F | U+2460–U+24FF |
            : U+2776–U+2793 | U+2C00–U+2DFF |
            : U+2E80–U+2FFF | U+3004–U+3007 |
            : U+3021–U+302F | U+3031–U+303F |
            : U+3040–U+D7FF | U+F900–U+FD3D |
            : U+FD40–U+FDCF | U+FDF0–U+FE1F |
            : U+FE30–U+FE44 | U+FE47–U+FFFD |
            : U+10000–U+1FFFD | U+20000–U+2FFFD |
            : U+30000–U+3FFFD | U+40000–U+4FFFD |
            : U+50000–U+5FFFD | U+60000–U+6FFFD |
            : U+70000–U+7FFFD | U+80000–U+8FFFD |
            : U+90000–U+9FFFD | U+A0000–U+AFFFD |
            : U+B0000–U+BFFFD | U+C0000–U+CFFFD |
            : U+D0000–U+DFFFD | U+E0000–U+EFFFD
    id_continue: `id_start` | 0-9 |
               : U+0300–U+036F | U+1DC0–U+1DFF |
               : U+20D0–U+20FF | U+FE20–U+FE2F

Not valid identifiers::

  9to5: false
  four-and-six: false
  four&six: false
  Why Not: false
  木.leaves: false

Valid identifiers::

  nineToFive: true
  four6: true
  _whoa_there: true
  กรุงเทพมหานคร: true
  🐱: true


.. _expressions:

Expressions
===========

.. highlight:: yaml

Plain :ref:`numbers` in JSON_ are useful for values that never change: version codes, fixed addresses, communication protocol constants, and so on.

By using *expressions*, the hardware model can represent a graph of related values with a finite space of allowable configurations. Expressions are great for clock rates, hardware settings, volume levels, or any other values that can change but only in carefully controlled ways.

Expressions are :ref:`strings` formatted according to a mini-language that can use :ref:`references` to link with expressions and values elsewhere in the model.

When an expression loads, that expression will resolve to a number right away, but that number may change at any time. As long as the expression is loaded, it has the ability to both observe and influence the other values it's linked to.

Much of the syntax below will seem familiar from other programming languages. Wiggleport expressions adopt a new lexical convention in which operators beginning with a colon (`:`) indicate constraints rather than boolean evaluation.

That got abstract fast, but here's an example. This is a YAML_ object modeling a simple baud_ rate generator. For this to parse, we'll need to know *a priori* that `baud` is an expression. The other expression `clock.rate` can be inferred by its mention in `baud`. ::

  # Model a clock generator that can tune
  # from 1 MHz to 5 MHz in 100 Hz steps

  clock:
    minimum_rate: 1000000
    maximum_rate: 5000000
    step_size: 100
    rate: (step_size * :int) :>= minimum_rate :<= maximum_rate

  # The divisor is an integer between 1 and 255, with no default

  divisor: :int :> 0 :< 0x100

  # Here the baud rate itself is calculated, and we set the default.
  # When this model loads, it will solve for the best configuration
  # to approximate 19200 baud.

  baud: clock.rate / divisor :~ 19200


Constants
---------

References
----------

Arithmetic Operators
--------------------

Constraint Operators
--------------------



Stream Objects
==============

Whereas values are passive unless they're referenced somehow, streams are objects that will be constructed automatically when a model loads. Wiggleport adopts a convention in JSON that a key beginning with "@" signifies that an object will be created when the corresponding part of the model loads.

In the abstract, a stream is an interface for flowing data. Data come packaged as *items* of a fixed or variable size in bits, at either a fixed or variable rate. Concretely, this interface may be backed either by a *buffer* or by a *pattern*.


Buffer Streams
--------------

A thing made of memory! Shared memory even.


Pattern Streams
---------------

State machines, yo.

.. _baud: https://en.wikipedia.org/wiki/Baud
.. _IEEE double: https://en.wikipedia.org/wiki/Double-precision_floating-point_format
.. _JSON: http://json.org
.. _YAML: http://yaml.org
.. _hexadecimal: https://en.wikipedia.org/wiki/Hexadecimal
.. _Unicode: https://en.wikipedia.org/wiki/Unicode

