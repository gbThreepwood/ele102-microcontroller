.. _phase_locked_loop:

****************************
Phase locked loop (PLL)
****************************

.. https://www.ti.com/lit/an/sprabt3a/sprabt3a.pdf
.. https://www.diva-portal.org/smash/get/diva2:1016340/FULLTEXT01.pdf
.. https://liquidsdr.org/blog/pll-howto/

.. https://github.com/jaspreetsingh009/Digital-Phase-Locked-Loop-PLL/tree/master/PLL%20Project/PLL/source

A phase locked loop (PLL) is a control loop which forces a locally generated signal to stay in phase with an external input signal. Since we are in control of our locally generated signal, it is more easy to extract information from it, when compared to the external signal. E.g. the frequency, and phase of our local signal can be used as measures of the frequency and phase of the external signal. The PLL can be realized in both software, and hardware, but in this lesson only software based PLLs are discussed.

The PLL has numerous applications, some of which include:

* Demodulation of FM
* Demodulation of FSK
* Clock multiplication or division
* Grid frequency synchronization for grid connected inverters 

One of the many applications of a phase locked loop is determination of the frequency, and phase of a measured waveform. If we need to measure the phase difference between two external signals, we can use two PLL's to determine the phase of each one, and compute the phase difference by subtraction.

Phase detector
==============

In general when multiplying two sine waves you will end up with two new sine waves a different frequencies. One will be at a frequency equal to the difference between the two original frequencies, while the other will be at the sum frequency.

We have the following trigonometric identity:

.. math::

    2\sin(a)\sin(b) = \cos(a - b) - \cos(a + b)

Consider the following expressions for a voltage, and a current:

.. math::

    v(t) = \hat{V} \sin(\omega t + \phi_{v})

.. math::

    i(t) = \hat{I} \sin(\omega t + \phi_{i})

By taking the product, and utilizing the previously mentioned trigonometric identity, we end up with the following result:

.. math::

    v(t)i(t) = \hat{V} \sin(\omega t + \phi_{v}) \hat{I} \sin(\omega t + \phi_{i}) = \frac{\hat{V}\hat{I}}{2} \left( \cos(\omega t + \phi_v - \omega t - \phi_i) - \cos(\omega t + \phi_v + \omega t + \phi_i) \right)
    
.. math::

    v(t)i(t) = \frac{\hat{V}\hat{I}}{2} \left( \cos(\phi_i - \phi_v) - \cos(2\omega t + \phi_v + \phi_i) \right)

The above expression reveals that the product of the two sinusoidal waveforms yields a new waveform which consists of one sine wave component at twice the frequency of the original waveforms (we assume the original waveforms have the same frequency), and another component which is a constant value proportional to the phase difference between the two waveforms.

By passing the result from the multiplication through a low pass filter (or possibly a notch filter which removes the component at twice the fundamental frequency), we will obtain a value equal to the cosine of the phase difference between the two waveforms.

.. math::

    \delta = \frac{1}{2} \cos(\phi_i - \phi_v)

The phase difference can thus be calculated by:

.. math::

    \varphi = \phi_i - \phi_v = \arccos(2 \delta)

It is important to realize that this is not the general case, it is consequence of the two waveforms having the same frequency, and only different phase. In reality there could be slight (or even large) differences in the frequency, and this phase detector is only a partial solution. In order to have a robust phase estimation we need a complete phase locked loop.

Notch filter
------------

A notch filter is a band stop filter with a high quality factor. I.e. it is a filter which will block a narrow range of frequencies. Since we know that the multiplication based phase detector will always output an unwanted sinusoidal component at twice the grid frequency, a notch filter becomes a logical choice to remove it.

The quality factor should not be too high, as this could mean that the filter is not effective in case of slight variations in the grid frequency. It is also possible to use an adaptive notch filter, where the filter parameters are varied in accordance with variations in the frequency of the measured waveform.

.. math::

    H(z) = \frac{b_0 + b_1 z^{-1} + b_2 z^{-2}}{a_0 + a_1 z^{-1} + a_2 z^{-2}}


The following code listing provides an example implementation of a notch filter:

.. code-block:: c

    ynotch[0] = -(a1 * ynotch[1]) - (a2 * ynotch[2]) + (b0 * pd[0]) + (b1 * pd[1]) + (b2 * pd[2]);
    
    // Update the phase detector array for the next iteration
    pd[2] = pd[1];
    pd[1] = pd[0];

    ynotch[2] = ynotch[1];
    ynotch[1] = ynotch[0];


As an alternative to the notch filter, or in combination with the notch filter, a low pass filter may be used. This will allow us to remove high frequency noise which might be present in the measured waveform.

Experimenting with the phase detector alone
-------------------------------------------

Despite it not being a robust way to estimate the phase difference in a production environment, it can still be rewarding to experience the operation of the phase detector outside the usage in a PLL, under ideal condition in a laboratory setup.

The following code listing shows how to sample and measure the phase difference between two 50 Hz sinusoidal waveforms. It is not optimized for efficiency, and it also could benefit from higher acquisition speed from the :code:`analogRead()` function, in order to have the two sine wave samples occurring simultaneously.

.. literalinclude:: ../../../projects/platformio/dsp/phase-detector-notch-filter/src/main.cpp
    :language: c
    :linenos:


Loop filter (PI controller)
===========================

The PLL is a control loop where we attempt to minimize the error in phase between our locally generated signal, and a measured signal. In practice the filter between our phase detector output, and our local oscillator input can often be realized as a PI-controller.

The controller may be implemented as a first order IIR filter:

.. math::

    y_n = a_1 y_{n - 1} + b_0 x_n + b_1 x_{n-1}

.. math::

    Y(z) = a_1 z^{-1} Y(z) + b_0 X(z) + b_1 z^{-1}X(z)

.. math::

    (1 - a_1 z^{-1}) Y(z) = (b_0 + b_1 z^{-1})X(z)

.. math::

    \frac{Y(z)}{X(z)} = \frac{b_0 + b_1 z^{-1}}{1 - a_1 z^{-1}}

The laplace domain representation of a PI-controller is given by:

.. math::

    H_{PI}(s) = K_p + \frac{K_i}{s}

.. math::

    z = e^{sT}

The bi-linear transformation is a linear first order approximation to the definition of z. It can be used to convert a continuous transfer function in to a discrete transfer function. It direct bilinear transformation is given by:

.. math::

    s = \frac{2}{T} \left( \frac{z - 1}{z + 1} \right)

The inverse bi-linear transformation is given by:

.. math::

    z = \frac{1 + \frac{T}{2}s}{1 - \frac{T}{2}s}

Where T is the sampling time.

By applying the bi-linear transformation to the transfer equation for the PI-controller we obtain:

.. math::

    H_{PI}(z) = K_p + \frac{K_i}{\frac{2}{T} \left( \frac{z - 1}{z + 1} \right)}



.. math::

    H(s) = \frac{\omega_n^2}{s^2 + 2\zeta\omega_n s + \omega_n^2}

One we have determined suitable gains for our PI-controller, the filter coefficients can be computed by:

.. math::

    b_0 = \frac{2K_p + K_i T}{2}

.. math::

    b_1 = -\frac{2K_p - K_i T}{2}


PI-controller implementation
----------------------------

.. code-block:: c

    ylf[0]= - (a1_lf * ylf[1]) + (b0_lf * x[0]) + (b1_lf * x[1]);

    // Update array for next iteration
    x[1] = x[0];
    ylf[1] = ylf[0];

Local oscillator
================

In an analog PLL the oscillator is often referred to as a voltage controlled oscillator (VCO). In the digital domain however the oscillator output is simply discrete numbers. The oscillator must be realized using trigonometric functions, simplified using other mathematical functions, or lookup tables.

Sine and cosine generation by integration
-----------------------------------------

The output from a function after a small change :math:`\Delta t` in the input can be expressed as:

.. math::

    y(t + \Delta t) = y(t) + \frac{\mathrm{d}y(t)}{\mathrm{d}t} \cdot \Delta t

For a sine, and a cosine function this yields:

.. math::

    sin(t + \Delta t) = \sin(t) + \cos(t) \cdot \Delta t

.. math::

    cos(t + \Delta t) = \cos(t) - \sin(t) \cdot \Delta t

This can be utilized to achieve a more efficient computation of the sine (and cosine) waveform for the local oscillator. We already know :math:`\sin(t)`, and :math:`\cos(t)` from our previous iteration, and thus the problem is reduced to some simple multiplications.

The following code listing illustrates how the sine wave generation is performed, and also how to extract the phase (theta). The code is written with floating point operations in mind, and should be adapted if fixed point calculation are needed instead:

.. code-block:: c

    // Obtain the required omega from the loop filter output
    wo = wn + y_pi[0];

    // Perform integration process to compute sine and cosine
    sin[0] = sin[1] + (delta_t * wo * cos[1]);

    cos[0] = cos[1] - (delta_t * wo * sin[1]);

    //...

    // Compute theta
    theta[0] = theta[1] + (wo * delta_t);
    //theta[0] = theta[1] + (wo * 0.15915494 * delta_t); // Normalize theta to pu by multiplication by 1/(2*pi)

    //...

    theta[1] = theta[0];

    // sin[0] is the sine value for the current iteration,
    // while sin[1] is the sine value for the previous iteration.
    sin[1] = sin[0];
    cos[1] = cos[0];

It should be noted that although the method is relatively efficient at generating a continuous waveform, it is not in general a good substitute for the slow sine and cosine functions available in various math libraries. The efficiency comes from the use in the iterative control loop, where the sine wave is approximated at small time steps. I.e. you do not know the sine of some arbitrary angle until you have stepped through all the other angles up to that point.


.. literalinclude:: ../../../projects/platformio/dsp/integration-sine-wave-generator/src/main.cpp
    :language: c
    :linenos:



Lookup table
------------

Since trigonometric operations are complex and often consume a large number of CPU cycles on a microcontroller, it is desirable to come up with a different approach to the generation of the local signal. One possibility is to use a lookup table with a large number of precalculated outputs from the trigonometric function.

An efficient way to implement a lookup table in terms of both memory usage and computation, is to split it in to two tables. One table for the discrete outputs of the sine function, and another to hold the difference (delta) between each discrete value. The discrete values in the table are equally spaced in degrees (or time), but will vary in spacing because the steepness of the sine wave curve varies. Having a separate table of the spacings will simplify the interpolation between each discrete element, as the fractional part of the table index can be multiplied by the delta to determine the interpolation value.

In practice it could be implemented as two tables of 256 elements, consuming 512 units of memory, where a unit could be 8, 16, or possibly 32 bit, depending on your requirements.

CORDIC
------

Coordinate rotation digital computer (CORDIC) is a efficient algorithm for calculation of various mathematical functions.


Sine approximation by quadratic functions
-----------------------------------------

The time values can be normalized to 0 to 1, by:

.. math::

    t = x \cdot \frac{1}{2\pi}

In order to wrap around after t reaches 1, we can subtract the integer version of t:

.. code-block::c

    t = t - (int)t


.. code-block::c

    float sine_approx(float theta){

        float t = theta * 1/(2*PI);

        t = t - (int)t;
        if(t < 0.5)
            return -16*t*t + 8*t;
        else
            return 16*t*t - 16*t - 8*t + 8;
    }

Example: Lock on a 5 Hz sine wave from a signal generator
=========================================================

.. literalinclude:: ../../../projects/platformio/dsp/grid-sync-pll/src/main.cpp
    :language: c
    :linenos:



Example: Lock on the power grid waveform sampled on the ADC
===========================================================

In this section we will consider the design of a PLL for locking on to a sine wave of 50 Hz, which is sampled by the ADC.

Grid frequency synchronization
==============================




