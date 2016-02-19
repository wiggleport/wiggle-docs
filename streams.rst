.. default-role:: literal

.. _stream-objects:

==============
Stream Objects
==============

In the abstract, a stream is just an interface for flowing data. Data come packaged as *items* of a fixed or variable size in bits, and these items flow either a fixed or variable rate. Wiggleport models hardware communication using networks of interconnected streams.

As an example, let's consider how streams could model communication with a common `NMEA GPS`_ receiver:

.. _NMEA GPS: https://en.wikipedia.org/wiki/NMEA_0183

=========================== ========================== ================== ===================
Stream Description          Item Description           Bits Per Item      Items Rate
=========================== ========================== ================== ===================
GPS messages                NMEA sentence string       variable           1 Hz
Serial port bytes           Byte value (0 to 255)      8                  480 Hz
Serial port bits            RS-232 encoded bits        1                  4800 Hz
=========================== ========================== ================== ===================

Even at this coarse level of detail, the model lets us make some useful inferences about timing. If the model is used to receive GPS messages at a constant rate, we can treat that one-per-second message rate as a clock source any time we need to measure time.

If the model is being used to transmit GPS messages, we already know how much time there is to send each NMEA message, and how long they can be at most. (480 bytes) In fact, this scheduling knowledge applies to the entire graph of related streams.

In the GPS receiver example, the model's streams would establish a data flow for processing and storing the incoming messages:

1. At the lowest level, an I/O driver blah blah.
2. Patterns
3. Stored in a buffer
4. API can do things

Concretely, this interface may be backed either by a *buffer* or by a *pattern*.


.. _buffer-streams:

Buffer Streams
==============

A thing made of memory! Shared memory even.


.. _pattern-streams:

Pattern Streams
===============

State machines, yo.
