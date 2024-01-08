.. DTMF_generator:

*****************
DTMF generator
*****************

.. Inspiration: http://ww1.microchip.com/downloads/en/AppNotes/doc1982.pdf

The following table provides the frequencies in the DTMF standard:

+--------+---------+---------+---------+---------+
|        | 1209 Hz | 1336 Hz | 1477 Hz | 1633 Hz |
+--------+---------+---------+---------+---------+
| 697 Hz | 1       | 2       | 3       | A       |
+--------+---------+---------+---------+---------+
| 770 Hz | 4       | 5       | 6       | B       |
+--------+---------+---------+---------+---------+
| 852 Hz | 7       | 8       | 9       | C       |
+--------+---------+---------+---------+---------+
| 941 Hz | \*      | 0       | #       | D       |
+--------+---------+---------+---------+---------+


.. math::
    f(t) = A_a \sin(\omega_a t) + A_b \sin(\omega_b t)

We also have in the standard that:

.. math::
    \frac{A_b}{A_a} = K

Where :math:`0.7 < K < 0.9`.


Reading a standard DTMF matrix keyboard
=======================================




Sine wave lookup table
======================

In order to efficiently determine the values of the sine function at each iteration a look up table is used. The fundamental sampling time used to step through the elements in this table sets the low limit on the output frequency. By skipping every other element in the table the frequency will be double, every third element will yield a frequency of three times the fundamental, and so on. This property can be exploited in order to generate a multitude of frequencies using a single lookup table, and a single sampling rate.

The DTMF standard consists of 8 frequencies, and thus we need 8 skipping values for our lookup table

Generate DTMF tones using PWM (Atmel app. note AVR314)
======================================================

The step with can be expressed as:

.. math::
    X_{sw} = f \frac{N_c}{f_s} 

Where :math:`f` is the frequency we would like to generate, :math:`N_c` is the number of samples in the lookup table, and :math:`f_s` is the sampling frequency, which in this implementation will correspond to the PWM frequency :math:`f_{sw}`.

If we are using one of the Atmeta328p 8 bit timers in symmetric PWM mode (up-down counting) it will use :math:`510` clock cycles to finish one counting period. Thus the sampling frequency will be given by:

.. math::
    f_s = \frac{510}{N_c}

Thus the step with is given by:

.. math::
    X_{sw} = f \frac{N_c}{f_s} = f \frac{N_c \cdot 510}{f_{clk}}

E.g. for a frequency of 1633 Hz, and a timer clock frequency of 2 MHz we have:

.. math::
    X_{sw} = f \frac{N_c \cdot 510}{f_{clk}} = 1633 \cdot \frac{128 \cdot 510}{2 \cdot 10^{6}} \approx 53

For the lower frequency of 697 we get:

.. math::
    X_{sw} = f \frac{N_c \cdot 510}{f_{clk}} = 697 \cdot \frac{128 \cdot 510}{2 \cdot 10^{6}} \approx 23

The Arduino UNO uses a clock frequency of 16 Mhz, and the timer prescaler is divide by 1, 8, 64, 256 or 1024. The only viable options are 1, or 8, but ideally we should have something in between. Alternatively the length of the lookup table can be adjusted. Furthermore the :math:`X_{sw}` values turns out to be fractions in reality, thus the accuracy can be increased by multiplying them by some value which allows us to keep some more bits (i.e. we use a fixed point representation).

The lookup table is selected to have 128 elements, and a maximum amplitude of 127. The reasoning for the maximum amplitude is that it will only use 7 bits, and thus there will be room for the sum of two waveforms in a 8 bit register.

The two lookup table indexes are given by:

.. math::
    X_{a,k} = X_{a,k-1} + X_{sw,a}

.. math::
    X_{b,k} = X_{b,k-1} + X_{sw,b}

Furthermore in accordance with the DTMF standard we need the amplitude of the *b* waveform to be less than the *a* waveform. The factor :math:`K = \frac{3}{4}`. The output compare value for the PWM timer is thus given by:

.. math::
    COMP(X) = LUT(X_{a,k-1} + X_{sw,a}) + \frac{3}{4} LUT(X_{b,k-1} + X_{sw,b})


.. math::
    X_{a,k} = \frac{1}{8} \left( X_{a-ext,k-1} + f_a \frac{8 N_c \cdot 510}{f_{clk}} \right)

.. math::
    X_{b,k} = \frac{1}{8} \left( X_{b-ext,k-1} + f_b \frac{8 N_c \cdot 510}{f_{clk}} \right)


The fraction of :math:`\frac{3}{4}` can be computed efficiently by bit shift and subtraction. Shifting two places to the left is the same as division by 4:

.. code-block:: c

    uint8_t comp_value = sine_wave_lut256[xlut_a] + (sine_wave_lut256[xlut_b] - sine_wave_lut256[xlut_b] >> 2);


Output filter and amplification
-------------------------------


Improved audio quality DTMF generation
======================================




Convert the digital values using 8-bit DAC
==========================================

The PWM method for digital to analog conversion has some issues. For one, unlike PCM, it is a non linear process.




Complete source code example
============================


.. literalinclude:: ../../../projects/platformio/audio/summing-opamp-dac-16-key-tones/src/main.cpp
    :language: c
    :linenos:

