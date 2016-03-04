.. default-role:: literal
.. _TRS Drawbot:  http://makezine.com/projects/trs-drawbot-2/
.. _GlueMotor: http://www.gluemotor.com/

.. _modeling-strategy:

=================
Modeling Strategy
=================

To interact with some hardware, a program needs some idea of what that hardware is capable of doing and how to communicate with it. Typically this knowledge is encoded into a specific stack of software, programming interfaces, and drivers.

To communicate with a sound device, for example, you can use a sound driver that fits into your operating system's way of handling audio. Other driver frameworks exist for graphics, disks, and so on. Typically writing a driver for a totally new kind of sound device would require specialized experience with each operating system and each driver framework you want to support.

This traditional approach offers some impediments to creativity and recombination. For example, let's consider the options for extending the existing audio stack to handle an output device that is also a dancing robot.

.. image:: /images/dancebot.*
   :alt: Dancing robot sketch
   :class: full-width-graphic

One option is to use the audio stack for everything, even the things that don't quite fit. With a multi-channel sound card, some of the sound channels could be used for actual sound, and some would be repurposed with signals for driving each of the robot's servo motors.

This approach would make it possible for the robot to keep exceptional rhythm! The signals for each motor could be synchronized perfectly with each sample of music, even taking into account the mechanical lag in each joint.

In fact, a stereo headphone jack can drive two servo motors without any additional active circuitry, just by generating audio waveforms that are also valid servo pulses. This approach comes from GlueMotor_, and the `TRS Drawbot`_ article explains it in detail.

One downside: it can be inconvenient to drive random electromechanical devices with audio hardware. The audio outputs aren't a great match for the servos. All but one of the 16 bits per sample are redundant, and while a 48 kHz rate makes for a perfectly listenable audio experience it's a bit choppy for controlling servos. Any more than two motors and you would need a multi-channel sound card, which can get expensive quickly.

Etc.

These models contain anything the computer needs to know about some hardware in order to communicate with it, including how to configure any programmable parts of that hardware. Wiggleport's modularity comes from the way these models can be freely reassembled without rewriting driver software.
