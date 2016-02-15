
==============
Hardware Model
==============

At it's very core, Wiggleport is based on a way of interacting with hardware by *modeling* the way data flows. These models contain anything the computer needs to know about some hardware in order to communicate with it, including how to configure any programmable parts of that hardware. Wiggleport's modularity comes from the way these models can be freely reassembled without rewriting driver software.

Like the Document Object Model you may be familiar with from web browsers, Wiggleport has a hardware model based on a tree-structured collection of objects. Whereas the web was born from the fairly complex standard that became XML, Wiggleport starts with something simpler: JSON_.


Modeling with JSON
==================

JSON_ itself is designed to be very simple and unambiguous for software to parse, but it can be inconvenient to read and write by hand. In describing hardware models, we will often use YAML_ as an alternate syntax for these same objects.

.. _JSON: http://json.org
.. _YAML: http://yaml.org


Numbers
-------

.. highlight:: json

JSON_ supports integers like ``524287`` and ``-42``, or floating point numbers like ``10.5`` and ``3e8``.  We will refer to these all as *numbers* usually. Integers are automatically promoted to floating point numbers as necessary. Integers are signed 64-bit numbers, and floating point values use 64-bit IEEE double precision.

.. highlight:: yaml

In modeling hardware, it's often helpful to use hexadecimal_ numbers. JSON does not support hexadecimal numbers, but both YAML_ and Wiggleport's :ref:`expressions` support hex numbers like ``0x2AF0`` and ``0x100000000``.

.. _hexadecimal: https://en.wikipedia.org/wiki/Hexadecimal


Strings
-------

Strings of Unicode_ characters can take on a variety of roles in Wiggleport's hardware models. Depending on context, a string may be parsed as an :ref:`expression <expressions>`, or it may represent freeform metadata like a device's name or its version.

.. _Unicode: https://en.wikipedia.org/wiki/Unicode

In JSON_, all strings must be surrounded with double-quotes. Much of YAML_'s readability comes from relaxing this requirement. Quotes can often be entirely omitted in YAML_, as the parser assumes any unintelligible data must be a string value.


Special Values
--------------

.. highlight:: json

JSON_ and YAML_ also both provide the boolean_ values ``true`` and ``false``, as well as the nothingness placeholder value ``null``.

.. _boolean: https://en.wikipedia.org/wiki/Boolean_algebra

.. highlight:: yaml

YAML_ lets you document your models using comment lines beginning with ``#``. JSON_ does not support comments at all, since it's designed for computers rather than humans.


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


Objects
-------

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

Identifiers
-----------


In Wiggleport's use of JSON, we assume every value within an object can be uniquely identified by its name. Values within nested objects can be identified using a dotted syntax. For example, ``"objects.etc.thing"`` could refer to the value ``99`` in the example above.

To be addressable using dot notation, JSON object names must contain 
 Names with spaces or dots will conflict with this addressing scheme.


.. _expressions:

Expressions
=================

Depending on context, part of the hardware model may be interpreted as a *value expression*. 

.. index:: pair: expression; value

Now talk about expressions, constraints, references, that kind of thing.

Constants
---------

References
----------

Operators
---------

.. _add:

\+ (add)
~~~~~~~~

woobly. :ref:`add` and such? or maybe :ref:`subtract` 


.. _subtract:

\- (subtract)
~~~~~~~~~~~~~

\* (multiply)
~~~~~~~~~~~~~

\/ (divide)
~~~~~~~~~~~

<< (left shift)
~~~~~~~~~~~~~~~

>> (right shift)
~~~~~~~~~~~~~~~~


Variables
---------

Constraints
-----------


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
