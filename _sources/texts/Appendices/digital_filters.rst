.. _digital_filters:

****************************
Basics of digital filters
****************************

.. http://ww1.microchip.com/downloads/en/AppNotes/Atmel-1631-Using-the-AVR-Hardware-Multiplier_ApplicationNote_AVR201.pdf
.. http://ww1.microchip.com/downloads/en/AppNotes/doc2527.pdf
.. https://www.hackster.io/bruceland/dsp-on-8-bit-microcontroller-21220c

Efficiency
==========

Floating point operations on the Atmega328p are generally very slow. Some mathematical operations, such as division, or trigonometric functions can take hundreds of clock cycles to complete. Thus floating point is is not a viable option for a digital filter running in real time, unless the frequencies of interest are very low. In order to overcome this issue one may employ fixed point numbers instead of floating point. Trigonometric functions, if needed, could be performed by means of lookup tables.

There are other microcontrollers which are better suited for digital signal processing, but with the aforementioned considerations even the Atmega328p can be used for many applications.

Moving average
==============


Finite impulse response (FIR) filter
====================================




Infinite impulse response (IIR) filter
======================================

.. https://www.advsolned.com/iir-filters-practical-guide/

.. Source: https://www.industrial-electronics.com/DAQ//understngng_dsp_6.html

A infinite impulse response (IIR) filter as the name implies, has a theoretically infinite impulse response. This is due to feedback from the output back in to the input of the filter. In practice the impulse response will die off after a while due to limitations to the smallest value that can be stored in the memory of the computer performing the filtering.

IIR filters are more complicated to analyze and design than FIR filters, but they are a lot more efficient. This means that for a given microcontroller or other signal processor, you will be able to achieve a much higher sample rate.

Lowpass filter (Floating point version)
---------------------------------------

As mentioned previously floating point operations are slow on the Atmega328p. Still they are easy to implement, and fast enough for a small filter with sampling rate of 1 kHz. The following filter is designed for low pass filtering of a 50 Hz sine wave, in order to remove any higher frequency components.

.. literalinclude:: ../../../projects/platformio/dsp/iir-low-pass-filter-float/src/main.cpp
    :language: c
    :linenos:



Notch filter
------------



Cascaded integrator comb (CIC)
==============================

A CIC filter is a type of FIR filter, where the coefficients are uniform. CIC filters are not a popular choice for general purpose filtering, but they are popular as an anti-aliasing filter before downsampling (decimation) of a signal to a lower sample rate.

Moving average
---------------

Decimation of a PDM signal from a digital MEMS microphone
---------------------------------------------------------


.. https://www.hackster.io/bruceland/dsp-on-8-bit-microcontroller-21220c