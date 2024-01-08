.. _induction_motor_control:

******************************************************************
Induction motor control
******************************************************************

.. role:: ccode(code)
        :language: c

Induction motor control theory
==============================

Constant U/f control
--------------------


Field oriented control
-----------------------


Direct torque control
---------------------


Microcontroller selection
==========================

Generation of three phase sinusoidal PWM at 50 Hz on the Arduino UNO (Atmega328p) is on the limit of the capabilities of the controller. For high performance motor control it would be preferable to have a microcontroller with special-purpose PWM generating capabilities. Still the exploration of the possibilities, and overcoming of the limitations is an interesting learning experience. Hence an attempt at using the Arduino UNO has been mande.

High performance microcontroller
--------------------------------

In addition to the Arduino UNO experiment, another experiment using a higher performance microcontroller has also been conducted. This controller allows us to experiment with closed loop control using FOC, or DTC. Furthermore it allows us to experiment with sensorless control strategies.


Custom 2-level three phase inverter
===================================

A custom 2-level three phase inverter is used.


Implementation of constant U/f control
======================================


