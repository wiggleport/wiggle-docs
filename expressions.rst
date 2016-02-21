.. default-role:: literal

.. _expressions:

=================
Expression Syntax
=================

.. highlight:: yaml

Regular :ref:`numbers` in the hardware model are useful for values that never change: version codes, fixed addresses, communication protocol constants, and so on.

With *expressions*, the hardware model can represent a graph of related values and determine their finite space of allowable configurations. Expressions are great for clock rates, hardware settings, volume levels, or any other values that can change but only in carefully controlled ways.

Expressions are :ref:`strings` formatted according to a mini-language that can use :ref:`references` to link with values and other expressions elsewhere in the model.

.. productionlist::
  expression: `reference` | `number` | `variable` | `enclosure`
  enclosure: "(" `expression` ")" |
           : `unary_operator` `expression` |
           : `expression` `binary_operator` `expression` |
           : `expression` "?" `expression` ":" `expression`

Every expression and subexpression can be evaluated to a number. Just like with JSON_ :ref:`numbers`, the internal storage can be either 64-bit signed integer or 64-bit `IEEE double`_ precision floating point. Promotion from integer to floating point happens as needed during arithmetic operations.


Life Cycle
==========

After an expression loads, it resolves to a number right away. That number may change at any time, and the object containing that expression will be notified of the new value. As long as the expression is loaded, it has the ability to both observe and influence the other values it's linked to.

Expressions in the model remain loaded as long as their corresponding portion of the model. Typically this is rooted in some physical hardware that we can detect as it's plugged in or unplugged.

In addition, temporary expressions may be created and destroyed explicitly using the API. They can be used as status observers, and temporary constraints can be thought of as requests at runtime to reconfigure the hardware in a particular way.


Examples
========

.. highlight:: yaml

That got abstract fast, but here's an example. This is a YAML_ object modeling a simple baud_ rate generator. For this to parse, we'll need to know *a priori* that `baud` is an expression. The other expressions `clock.rate` and `divisor` can be inferred by their mentions in `baud`. ::

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

With this model, the baud generator will default to exactly 19200 baud. The constraints are quite loose at this point, and many equivalent configurations are available to choose from after we reach the minimum `clock.rate` of 1 MHz:

============ ========== ==========
clock.rate   divisor    baud
============ ========== ==========
1017600      53         19200
1036800      54         19200
1056000      55         19200
...          ...        ...
4896000      255        19200
============ ========== ==========

When the hardware model loads, one of these configurations will be chosen arbitrarily. Now imagine an application arrives and wants to configure the baud rate for something higher. Using the API, it loads a temporary expression like ``baud := 115200``. Now the list of valid configurations has changed, and the hardware will reconfigure to an arbitrary rate from this new set:

============ ========== ==========
clock.rate   divisor    baud
============ ========== ==========
1036800      9          115200
1152000      10         115200
1267200      11         115200
...          ...        ...
4953600      43         115200
============ ========== ==========


.. _expression-constants:

Constants
=========

.. highlight:: yaml

.. productionlist::
  number: `decimal_integer` | `hex_integer` |
        : `octal_integer` | `binary_integer` |
        : `real_number`

The simplest expression is a *constant*, serving the same function as plain JSON :ref:`numbers`. These values can be relied on to never change unless that part of the model is reloaded. Each numeric constant in an expression may use decimal, hexadecimal_, octal, binary, or floating point notations.

.. productionlist::
  digit_sep: "_"?

In numeric constants, underscore characters may be used to visually separate digits.

.. productionlist::
  negative: "-"?
  decimal_integer: `negative` "0" |
                 : `negative` 1-9 ( `digit_sep` 0-9 )*

Examples::

  0
  -0
  42
  -100_000
  1_2_300

Note that leading zeroes are not allowed in decimal constants, to prevent ambiguity with a common method of writing octal constants in C-like langauges.

.. productionlist::
  hex_prefix: "0x" | "0X"
  hex_digit: 0-9 | a-f | A-F
  hex_integer: `negative` `hex_prefix` `hex_digit` ( `digit_sep` `hex_digit` )*

Examples::

  0x4a42_0D9C_9944abcd
  0X04
  -0x2000

.. productionlist::
  oct_prefix: "0o" | "0O"
  oct_digit: 0-7
  octal_integer: `negative` `oct_prefix` `oct_digit` ( `digit_sep` `oct_digit` )*

Examples::

  0o477
  -0O0010_4000

.. productionlist::
  binary_prefix: "0b" | "0B"
  binary_integer: `negative` `binary_prefix` 0-1 ( `digit_sep` 0-1 )*

Examples::

  0b01010101
  -0B100
  0b1101_0111_10000000_11111110

.. productionlist::
  exponent_prefix: "e" | "E"
  sign: "+" | "-"
  digits: 0-9 ( `digit_sep` 0-9 )*
  real_exponent: `exponent_prefix` `sign`? `digits`
  real_mantissa: `negative` `digits`? "." `digits` |
               : `negative` `digits` "."
  real_number: `real_mantissa` `real_exponent`? |
             : `decimal_integer` `real_exponent`

Examples::

  10.
  .5
  0.550_291
  100_421.5
  1e200
  5.2e1_5


.. _expression-references:

References
==========

When the expression parser encounters something that looks like a :token:`reference` token, it will immediately resolve that reference to a specific JSON_ object in the model. After this point, the reference remains intact as long as both involved expressions are loaded into the model.

If the reference cannot be resolved, or it resolves to something other than a number or a valid expression string, this will cause an error immediately.

.. highlight:: yaml

Example constants and references, in a YAML_ object::

  sample_constants:
    just_a_string: This will not be parsed as an expression

    the_answer: 42
    physics:
      speed_of_light: 2.99792e8

  sample_refs:
    # References can be arbitrarily deep, so long as the
    # final target is a number or expression.

    my_speed: sample_constants.physics.speed_of_light

    # This is parsed as an expression if and only if
    # "still_the_same_answer" below is an expression.

    also_the_answer: sample_constants.the_answer

    # This will evaluate to a constant "42"

    still_the_same_answer: also_the_answer


.. _arithmetic-opers:

Arithmetic Operators
====================

Expressions can be new values computed from multiple existing values, using many of the same unary and binary operators you may know from other programming languages. Each of these expressions sets up a *data flow*, where changes to the inputs will automatically cause an observable change in the expression's result.

+------------+------------------------+------------------+-------------------+-----------------+
| Precedence | Description            | Operator         | Operand Type(s)   | Result Type     |
+============+========================+==================+===================+=================+
| 1          | Negate                 | `-a`             | Integer / Real    | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Bitwise Complement     | `~a`             | Integer           | Integer         |
|            | [#cpl]_                |                  |                   |                 |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Logical Inverse        | `!a`             | Integer / Real    | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
| 2          | Exponentiate           | `a ** b`         | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
| 3          | Multiply               | `a * b`          | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Divide                 | `a / b`          | Integers / Reals  | Real            |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Integer Divide         | `a // b`         | Integers / Reals  | Integer         |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Modulo [#mod]_         | `a % b`          | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Divisor Modulo [#rem]_ | `a %% b`         | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
| 4          | Add                    | `a + b`          | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Subtract               | `a - b`          | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
| 5          | Left Shift             | `a << b`         | Integers          | Integer         |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Right Shift            | `a >> b`         | Integers          | Integer         |
+------------+------------------------+------------------+-------------------+-----------------+
| 6          | Less Than              | `a < b`          | Integers / Reals  | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Less Than or Equal     | `a <= b`         | Integers / Reals  | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Greater Than           | `a > b`          | Integers / Reals  | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Greater Than or Equal  | `a >= b`         | Integers / Reals  | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
| 7          | Equality Test          | `a == b`         | Integers / Reals  | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
|            | Inequality Test        | `a != b`         | Integers / Reals  | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
| 8          | Bitwise AND            | `a & b`          | Integers          | Integer         |
+------------+------------------------+------------------+-------------------+-----------------+
| 9          | Bitwise XOR            | `a ^ b`          | Integers          | Integer         |
+------------+------------------------+------------------+-------------------+-----------------+
| 10         | Bitwise OR             | `a | b`          | Integers          | Integer         |
+------------+------------------------+------------------+-------------------+-----------------+
| 11         | Logical AND            | `a && b`         | Integers          | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
| 12         | Logical OR             | `a || b`         | Integers          | 0 or 1          |
+------------+------------------------+------------------+-------------------+-----------------+
| 13         | Conditional [#cond]_   | `a ? b : c`      | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+
| 14         | Comma [#comma]_        | `a, b`           | Integers / Reals  | Integer / Real  |
+------------+------------------------+------------------+-------------------+-----------------+

.. [#cpl] Bitwise complement `~a` is equivalent to `a ^ 0xFFFF_FFFF_FFFF_FFFF`.
.. [#mod] The result in Modulo takes the sign of `a`.
.. [#rem] The result in Divisor Modulo takes the sign of `b`.
.. [#cond] The ternary_ conditional `a ? b : c` evaluates `a`, choosing to return `b` if nonzero and `c` if zero.
.. [#comma] The comma operator `a, b` evaluates both expressions, but keeps only value `b`. The expression `a` may still contribute by including expression references or constraints.


.. _constraint-opers:

Constraint Operators
====================

Wiggleport uses a system of *constraints* for modeling the relationship between hardware capabilities and requirements. Operators and keywords beginning with a colon (`:`) are related to constraints.

The constraint solver might support new basic types in the future, but right now we're focused on hardware with discrete configuration states. Our basic *variable* type is an integer:

.. productionlist::
  variable: ":int"

Variables have no default value and no specific range of valid values. Potential and current values for each variable will be determined based on the network of expressions attached to that variable. All of the :ref:`arithmetic-opers` work on variables, as well as a new category of constraint operators:

+------------+------------------------------------+---------------+----------------------+--------+
| Precedence | Description                        | Operator      | Operand Type(s)      | Value  |
+============+====================================+===============+======================+========+
| 15         | Constrain to Less Than             | `a :< b`      | Ints / Reals / Vars  | `a`    |
+------------+------------------------------------+---------------+----------------------+--------+
|            | Constrain to Less Than or Equal    | `a :<= b`     | Ints / Reals / Vars  | `a`    |
+------------+------------------------------------+---------------+----------------------+--------+
|            | Constrain to Greater Than          | `a :> b`      | Ints / Reals / Vars  | `a`    |
+------------+------------------------------------+---------------+----------------------+--------+
|            | Constrain to Greater Than or Equal | `a :>= b`     | Ints / Reals / Vars  | `a`    |
+------------+------------------------------------+---------------+----------------------+--------+
|            | Constrain to Equality              | `a := b`      | Ints / Reals / Vars  | `a`    |
+------------+------------------------------------+---------------+----------------------+--------+
|            | Weak Equality Constraint [#weak]_  | `a :~ b`      | Ints / Reals / Vars  | `a`    |
+------------+------------------------------------+---------------+----------------------+--------+

.. [#weak] Weak constraints do not require exact equality, and they will yield to a strong equality constraint or a conflicting inequality. The weak constraint operator is useful for specifying a default or nominal value.

Conflicts in strong constraints are disallowed entirely. If a model can't meet all constraints, it will be unable to load. Changes will be prohibited if they violate any constraints irreconcilably.

If multiple weak constraints apply to the same variable, they will be prioritized by their distance from this variable in the expression graph. Weak constraints farther from a variable can override weak constraints closer to the same variable.


.. _ternary: https://en.wikipedia.org/wiki/Ternary_operation
.. _baud: https://en.wikipedia.org/wiki/Baud
.. _IEEE double: https://en.wikipedia.org/wiki/Double-precision_floating-point_format
.. _JSON: http://json.org
.. _YAML: http://yaml.org
.. _hexadecimal: https://en.wikipedia.org/wiki/Hexadecimal
