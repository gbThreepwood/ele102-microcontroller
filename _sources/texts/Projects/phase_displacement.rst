.. _phase_displacement_measurement:

******************************************************************
Phase displacement measurements
******************************************************************

.. role:: ccode(code)
        :language: c


Instantaneous magnitude difference
----------------------------------

If two signals have equal amplitude the displacement can be measured as the difference in magnitude between samples of each waveforms taken at the same instant. E.g. by subtracting one sine wave from the other, a new signal will emerge, with zero amplitude for zero phase displacement.

Peak or zero crossing detector
-------------------------------


.. source: http://www.energialternativa.org/yabbfiles/Attachments/16th_article.pdf

Discrete fourier transform (DFT)
--------------------------------

If you know the frequency of the signal that you would like to measure, a single frequency DFT can be used to detect the phase difference.

.. source: https://www.embeddedrelated.com/showthread/comp.arch.embedded/54443-1.php
.. source: https://dsp.stackexchange.com/questions/3301/is-there-an-algorithm-to-compute-the-phase-for-a-single-frequecy


For each of the signals the following equations will provide the phase relative to a reference:

.. math::

    x_i = \sum_{n = 0}^{N - 1} \cos(f \cdot t_n) \cdot x_n


.. math::

    x_q = \sum_{n = 0}^{N - 1} \sin(f \cdot t_n) \cdot x_n

.. math::

    \theta = \textnormal{arctan2}(x_i, x_q)


If both signals have the same reference, the phase difference can be computed by:

.. math::

    \varphi = \theta_1 - \theta_2

The reference for each signal will be the instant we start the sampling. This is not important however, since we are only looking at the difference. In order to avoid confusion it can be advantageous to normalize the angle :math:`\varphi` from -180 to 180 degrees. Otherwise the computation might sometimes produce results such as -340, instead of +20. An approximately steady phase displacement should not have such large swings in numerical value, even though the value represents the same angle.

The following code listing illustrates an implementation for the sampling and calculation of the phase displacement between two 50 Hz waveforms.

.. literalinclude:: ../../../projects/platformio/dsp/dft-phase-difference-estimation/src/main.cpp
    :language: c
    :linenos:

