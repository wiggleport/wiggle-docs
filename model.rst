.. default-role:: literal
.. _TRS Drawbot:  http://makezine.com/projects/trs-drawbot-2/
.. _GlueMotor: http://www.gluemotor.com/

.. _modeling-strategy:

=================
Modeling Strategy
=================

Wiggleport's **model** is the common language that drivers and applications can use to interact with hardware together.

This strategy is designed to work even if the hardware's configuration isn't known ahead of time, and you'd like to create new hardware modules without writing new driver code.


Nobody Likes Drivers
====================

Okay, it's not entirely true. I like drivers sometimes. But usually drivers are not so fun to make. They're different for every operating system and often particular to a each framework within that OS. It's usually impractical to write drivers in high-level languages like Javascript, and in fact it's typically necessary to be especially careful with resource usage and correctness when writing such low-level code.

Wiggleport tries to help you do new things with hardware without writing new drivers. In particular, this model layer is designed to help bridge the gaps between these different types of driver stacks.


How to Robot
============

To think more concretely about the options for remixing your own real-time hardware, let's have a case study. Consider adding a real-time *dancing robot* to a sound system that already plays interactive music.

.. image:: /images/dancebot.*
   :alt: Dancing robot sketch
   :class: full-width-graphic


Autonomous Systems
------------------

In this case, it may be an option to keep the systems for the robot and the music totally separate. The music plays on its own, interacts on its own, and the robot's algorithms hear the music at the same time it reaches the speakers.

The hardware for this system can be relatively simple, and there's no limit to how it could scale. If you build one autonomous robot, you can probably build a hundred without any software architecture issues.

The robot's hardware and algorithms would both introduce some lag. It could still synchronize with the beat, but it wouldn't necessarily react right away if the music changes pace.

GETTING THERE BUT THIS CLEARLY NEEDS WORK

One option is to use the audio stack for everything, even the things that don't quite fit. With a multi-channel sound card, some of the sound channels could be used for actual sound, and some would be repurposed with signals for driving each of the robot's servo motors.

This approach would make it possible for the robot to keep exceptional rhythm! The signals for each motor could be synchronized perfectly with each sample of music, even taking into account the mechanical lag in each joint. In fact, a stereo headphone jack can drive two servo motors without any additional circuitry, just by generating audio waveforms that are also valid servo pulses. This approach comes from GlueMotor_, and the `TRS Drawbot`_ article explains it in detail.

There are some downsides. It's inconvenient to drive random devices with audio hardware, and the audio outputs aren't a perfect match for servos. All but one of the 16 bits per sample are redundant, and while a 48 kHz rate makes for a perfectly listenable audio experience it's a bit choppy for controlling servos. Any more than two motors and you would need a multi-channel sound card, which usually means buying studio-grade audio equipment. Sometimes this is what you want, but it can get expensive quickly and the opportunities for customization are limited.

* Generic Devices
* Reusable Drivers
* Modular Hardware

These models contain anything the computer needs to know about some hardware in order to communicate with it, including how to configure any programmable parts of that hardware. Wiggleport's modularity comes from the way these models can be freely reassembled without rewriting driver software.
