.. default-role:: literal


==============
Stream Objects
==============

Whereas values are passive unless they're referenced somehow, streams are objects that will be constructed automatically when a model loads. Wiggleport adopts a convention in JSON that a key beginning with "@" signifies that an object will be created when the corresponding part of the model loads.

In the abstract, a stream is an interface for flowing data. Data come packaged as *items* of a fixed or variable size in bits, at either a fixed or variable rate. Concretely, this interface may be backed either by a *buffer* or by a *pattern*.


Buffer Streams
==============

A thing made of memory! Shared memory even.


Pattern Streams
===============

State machines, yo.

.. _ternary: https://en.wikipedia.org/wiki/Ternary_operation
.. _baud: https://en.wikipedia.org/wiki/Baud
.. _IEEE double: https://en.wikipedia.org/wiki/Double-precision_floating-point_format
.. _JSON: http://json.org
.. _YAML: http://yaml.org
.. _hexadecimal: https://en.wikipedia.org/wiki/Hexadecimal
.. _Unicode: https://en.wikipedia.org/wiki/Unicode

