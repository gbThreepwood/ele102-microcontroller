
************************************
Designing a digital piano
************************************

.. http://ww1.microchip.com/downloads/en/AppNotes/doc2527.pdf

8-bit digital to analog converter
=================================

By using multiple digital outputs and a operational amplifier which sums the outputs using different weights, it is possible to convert a digital number to an analog voltage. Here we will use a single 8-bit output register, and a summing amplifier with 8 inputs.


Summing amplifier
-----------------

The digital output voltage is either 0 V, or 5 V. We would like to be able to create a voltage which can be both positive and negative.


Analog write function
---------------------

In order to easily use our new custom DAC hardware a special function has been prepared:

.. code-block:: c

    DAC_Write(uint8_t value);


Keyboard hardware
==================

In a real piano the intensity of the sound is dependent on how hard you are hitting the keys.

Generation of notes
====================

Sinusoidal waveforms and look up tables
----------------------------------------

In order to efficiently create sinusoidal waveforms a look up table (LUT) has been used.

Musical scales and frequency tables
-----------------------------------

Karplus-Strong Algorithm
========================

The Karplus-Strong algorithm is a form of digital waveguide synthesis algorithm, which is particularly suitable for the synthesis of a sound which resembles the sound of a hammered or plucked string. E.g. the string in a guitar or a piano.

.. https://www.hackster.io/bruceland/dsp-on-8-bit-microcontroller-21220c