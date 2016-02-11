==============
Hardware Model
==============

At it's very core, Wiggleport is based on a way of interacting with hardware by *modeling* the way data flows. These models contain anything the computer needs to know about some hardware in order to communicate with it, including how to configure any programmable parts of that hardware. Wiggleport's modularity comes from the way these models can be freely reassembled without rewriting driver software.

Like the Document Object Model you may be familiar with from web browsers, Wiggleport has a hardware model based on a tree-structured collection of objects. Whereas the web was born from the fairly complex standard that became XML, Wiggleport starts with something simpler: JSON_.


Modeling with JSON
==================

Here's a review of what a standard JSON_ object might hold:

.. _JSON: http://json.org

.. js:class:: foo(blah)

.. js:class:: foo blah blah right

.. js:class:: blurrrr

.. js:attribute:: foo.blobble

    yeb.

.. js:attribute:: foo.wudgits

    yeb.

.. js:attribute:: blurrrr.minny.flub

    yeb.

.. code-block:: json

    {
        "wibble": "wubble"
    }

.. code-block:: yaml

    #
    # Testing out some YAML syntax hilighting too, yep!
    #

    name: wiggle-out
    version: 1.0.0
    description: Wiggle Out is a 2-channel class D amplifier
    homepage: http://wiggleport.org
    license: MIT
    files:
      - wiggle-out.v

    class: wigglemod
    prototype:
        wubbalubba: dub dub


Values that Change
==================

Now talk about expressions, constraints, references, that kind of thing.


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
