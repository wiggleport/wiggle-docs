
.. image:: /images/wigglemod-spec.*
   :width: 100%
   :alt: Wigglemod Spec Diagram

.. _wigglemod:

================
Module Connector
================

.. figure:: /images/68021-220HLF.*
   :figwidth: 30 %
   :alt: Photo of Amphenol 68021-220HLF
   :align: right

   Male dual-row right angle 2.54mm header.

Each module connector is a right angle **dual-row** female header, with **20 contacts** on 2.54mm / 0.1" centers. These headers are compatible with common prototyping jumpers, and they're available from many sources in both surface-mount and through-hole varieties.

Sample mating connectors for use in modules:

+--------------------------+------------------------------+
| Product                  | Distributor                  |
+==========================+==============================+
| Amphenol 68021-220HLF    | `Digi-Key 609-3347-ND`_      |
|                          +------------------------------+
|                          | `Mouser 649-68021-220HLF`_   |
+--------------------------+------------------------------+
| Molex 71764-0120         | `Mouser 538-71764-0120`_     |
+--------------------------+------------------------------+

.. _Digi-Key 609-3347-ND: http://www.digikey.com/product-detail/en/68021-220HLF/609-3347-ND/1878576
.. _Mouser 649-68021-220HLF: http://www.mouser.com/ProductDetail/FCI/68021-220HLF/
.. _Mouser 538-71764-0120: http://www.mouser.com/ProductDetail/Molex/71764-0120/

.. figure:: /images/centronic-230-XX.*
   :width: 80 %
   :alt: Mechanical drawing for the Centronic 230-20
   :align: center

   Mechanical drawing for the female headers used on the Wiggle Spine board.


.. _pinout-table:

Pinout Table
============

The pin numbering alternates between rows, starting with pin 1 identified using both a printed arrow and a square solder pad.

+---------------------+-------+-------+---------------------+
| Function →          | Pin   | Pin   | ← Function          |
+=====================+=======+=======+=====================+
| GPIO [#gpio]_       | 1     | 2     | GPIO                |
+---------------------+-------+-------+---------------------+
| GPIO                | 3     | 4     | GPIO                |
+---------------------+-------+-------+---------------------+
| GPIO                | 5     | 6     | GPIO                |
+---------------------+-------+-------+---------------------+
| GPIO / SCL [#det]_  | 7     | 8     | GPIO / SDA          |
+---------------------+-------+-------+---------------------+
| Ground              | 9     | 10    | Ground              |
+---------------------+-------+-------+---------------------+
| 3.3V                | 11    | 12    | 3.3V                |
+---------------------+-------+-------+---------------------+
| 5V                  | 13    | 14    | 5V                  |
+---------------------+-------+-------+---------------------+
| 5-24V (-) [#pgnd]_  | 15    | 16    | 5-24V (-)           |
+---------------------+-------+-------+---------------------+
| 5-24V (-)           | 17    | 18    | 5-24V (+)           |
+---------------------+-------+-------+---------------------+
| 5-24V (+)           | 19    | 20    | 5-24V (+)           |
+---------------------+-------+-------+---------------------+

.. [#gpio] All General Purpose I/O pins are 3.3V LVCMOS compatible. Inputs are not 5V tolerant!

.. [#det] Pins 7 and 8 are usable as normal GPIOs after a module has been detected. During detection, modules must tolerate arbitrary outputs on these pins as the various available JSON packages try to probe this module's identity. Pin assignments for I²C are suggestions only. Series resistors are recommended on these pins to prevent output contention.

.. [#pgnd] The 5-24V power supply has a separate ground return available, in order to reduce noise currents through the main digital ground.


.. _pmod-compat:

PMOD Compatibility
==================

Let's talk pmods, mkay!


.. _electrical-characteristics:

Electrical Characteristics
==========================

+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| Symbol                      | Parameter Description       | Minimum                     | Typical                     | Maximum                     | Units |
+=============================+=============================+=============================+=============================+=============================+=======+
| V\ :subscript:`EXT`         | External supply voltage     | 4.55                        | 5 - 24                      | 24.5                        | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`CC5`         | 5V supply voltage           | 4.5                         | 5.0                         | 5.5                         | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`CC3`         | 3.3V supply voltage         | 3.14                        | 3.30                        | 3.46                        | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`IN`          | Input voltage applied       | -0.5                        |                             | 3.60                        | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`HBM`         | ESD Tolerance               |                             | >2                          |                             | kV    |
|                             | (Human Body)                |                             |                             |                             |       |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`CDM`         | ESD Tolerance               |                             | >1                          |                             | kV    |
|                             | (Charged Device)            |                             |                             |                             |       |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`IL`          | Input low level             | -0.3                        |                             | 0.8                         | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`IH`          | Input high level            | 2.0                         |                             | V\ :subscript:`CC3` + 0.2   | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`OL`          | Output low level            |                             |                             | 0.4                         | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| V\ :subscript:`OH`          | Output high level           | V\ :subscript:`CC3` - 0.4   |                             |                             | V     |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| I\ :subscript:`OL`          | Output sink current         |                             |                             | 8                           | mA    |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| I\ :subscript:`OH`          | Output source current       |                             |                             | 8                           | mA    |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| I\ :subscript:`EXT`         | External supply current     |                             |                             | 4                           | A     |
|                             | [#exti]_                    |                             |                             |                             |       |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| I\ :subscript:`CC5`         | 5V supply current           |                             |                             | 100                         | mA    |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+
| I\ :subscript:`CC3`         | 3.3V supply current         |                             |                             | 100                         | mA    |
+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-----------------------------+-------+

.. [#exti] The maximum rating I\ :subscript:`EXT` assumes all pins 15-20 are sharing the current load. Note that all 7 modules cannot draw this maximum simultaneously, due to input power limits.


.. _mechanical-characteristics:

Mechanical Characteristics
==========================

Bloop!

