.. Kalman_filter:

*****************
Kalman filter
*****************

A Kalman filter uses a iterative mathematical process to quickly estimate the true value of some measurement which contains random noise or uncertainty.

If you have a measurement which varies due to noise but in reality is constant, the average of the measurement will likely be closer to the real value than any given sample. The averaging process however will require a lot of samples before it stabilizes close to the real value, with a Kalman filter this will be much faster.

Single value estimation
------------------------

The Kalman filter is effective for estimation of multiple values simultaneously, but initially we will describe how to estimate a single value.

- Calculate the Kalman gain
- Calculate the current estimate
- Calculate the uncertainty (often referred to as the error) in the estimate.


The Kalman gain is the ratio of the error in the estimate, to the sum of the errors in estimate and measurement.

.. math::

    K_k = \frac{\epsilon_{est}}{\epsilon_{est} + \epsilon_{meas}}

If the Kalman gain :math:`K_k` is high (close to 1) it means that our measurements are accurate with respect to our estimates. I.e. the fraction is approximately equal to:

.. math::
    
    K_k = \frac{\epsilon_{est}}{\epsilon_{est} + \epsilon_{meas}} \approx \frac{\epsilon_{est}}{\epsilon_{est}}  

If this is the case we put more emphasis on the measurements than we do on the previous estimate. The estimate for the current iteration is computed by:

.. todo: verify that zk is really the standard symbol for the measurement

.. math::

    \hat{x}_k = \hat{x}_{k - 1} + K_k (z_k - \hat{x}_{k - 1})

Where :math:`z_k` is the measurement for the current iteration, :math:`\hat{x}_k` is the estimate for the current iteration, and :math:`\hat{x}_{k - 1}` is the estimate for the previous iteration. A high Kalman gain means that we consider the difference between measurement and previous estimate to be important, and allow it to have a large impact on our current estimate. As the estimate improves however, we put less emphasis on the difference between estimate and measurement.


.. math::

    \epsilon_{est(k)} = (1 - K_k) \epsilon_{est(k - 1)}


Temperature measurement using single sensor
-------------------------------------------


Multi dimensional models
------------------------

.. math::

    x_k = A x_{k - 1} + B u_k + \omega_k

E.g. a state matrix for position and velocity in the x direction could look like:

.. math::

    x =
    \begin{bmatrix}
    x \\
    \dot{x}
    \end{bmatrix}


.. math::

    x =
    \begin{bmatrix}
    x \\
    y \\
    z \\
    \dot{x} \\
    \dot{y} \\
    \dot{z}
    \end{bmatrix}



Variance - covariance matrix
----------------------------

The variance is the square of the standard deviation for at set of measurements.

.. math::

    \sigma_x^2 = \frac{\sum_{i = 1}^{N} (\bar{x} - x_i)^2}{N}

.. math::

    \sigma_x \sigma_y = \frac{\sum_{i = 1}^{N} (\bar{x} - x_i)(\bar{y} - y_i)}{N}


:math:`[P]` is the state covariance matrix (error in the estimate), and :math:`[Q]` is the process noise covariance matrix. The process noise covariance matrix is important in order to ensure that the state covariance matrix does not become to small, which in turn would make our filter put to high emphasis on the estimation rather than the measurement.


:math:`[R]` is the measurement covariance matrix (error in the measurement), and :math:`[K]` is the Kalman gain.


Temperature measurement using multiple sensors
----------------------------------------------

In this example we will combine the measurements from multiple temperature sensors, in order to obtain a better estimate of the actual temperature than we would obtain from the filtered value of a single temperature sensor.
