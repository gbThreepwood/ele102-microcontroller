*******************************************************
Additional material for lesson 10
*******************************************************

.. _transfer_function:

Transfer function
-----------------

The transfer function is very useful when analyzing the transient behavior of the motor. By using the laplace transform, we arrive at the following results for the mechanical and electrical differential equations:

.. math::

    s(Js + B) \theta(s) = k_T I_a(s) \Rightarrow s^2J \theta(s) + sB\theta(s) = k_T I_a(s)

.. math::

    (R_a + sL_a) I_a(s) = V_a(s) - s K_e \theta(s) \Rightarrow (R_a + sL_a) I_a(s) = V_a(s) - K_e \omega_m(s)

.. math::

    s J \omega_m(s) + B \omega_m(s) = k_T I_a(s) \Rightarrow (sJ + B) \omega_m(s) = k_T I_a(s)



A transfer function relates some input to some output. We have several possible transfer functions for the DC motor depending on the value which is of interest.

* The motor speed for a given armature voltage
* The motor speed for a given torque
* The motor armature current for a given armature voltage
* The motor armature current for a given torque

The armature current can be expressed as:

.. math::
    I_a(s) = \frac{V_a(s) - k_e \omega_m(s)}{R_a + sL_a}

The armature angular velocity can be expressed as:

.. math::
    \omega_m(s) = \frac{k_T I_a(s) - T_L}{sJ + B}

Neglecting the load torque :math:`T_L` and remembering that :math:`k_T = k_e = k`, we can write:

.. math::
    \omega_m(s) = \cfrac{k_T \cfrac{V_a(s) - k_e \omega_m(s)}{R_a + sL_a}}{sJ + B} = \frac{k V_a(s) - k^2 \omega_m(s)}{(R_a + sL_a)(sJ + B)} 

We want to solve for :math:`\frac{\omega_m}{V_a}`, we start by isolating :math:`\omega_m`:

.. math::
    \frac{\omega_m}{k} \cdot (R_a + sL_a)(sJ + B) + k \omega_m = V_a \Rightarrow \omega_m \left(\frac{(R_a + sL_a)(sJ + B)}{k} + k \right) = V_a

Multiplying by :math:`\frac{k}{k}` in the second term we can write: 

.. math::
    \omega_m \left(\frac{(R_a + sL_a)(sJ + B) + k^2}{k} \right) = V_a

Thus the transfer function between speed and applied voltage is given by:

.. math::
    H_{\omega}(s) = \frac{\omega_m(s)}{V_a(s)} = \frac{k}{(Js + B)(Ls + R) + k^2}

Similar derivations can be performed for the other transfer functions if needed.

By choosing the mechanical angular velocity and the armature current as state variables, we arrive at the following state-space representation for the motor:

.. math::

    \frac{\mathrm{d}}{\mathrm{d}t}
    \begin{bmatrix}
    \omega_m \\
    i_a
    \end{bmatrix}
    =
    \begin{bmatrix}
    -\frac{B}{J} & \frac{K}{J} \\
    -\frac{K}{L} & -\frac{R}{L}
    \end{bmatrix}
    \begin{bmatrix}
    \omega_m \\
    i_a
    \end{bmatrix}
    +
    \begin{bmatrix}
    0 \\
    \frac{1}{L} 
    \end{bmatrix}
    V_a

.. math::

    y =
    \begin{bmatrix}
    1 & 0    
    \end{bmatrix}
    \begin{bmatrix}
    \omega_m \\
    i_a    
    \end{bmatrix}


It can be insightful to define and use time constants in the transfer function expressions. A mechanical time constant can be expressed as:

.. TODO: Verifiser denne:

.. math::

    \tau_m = \frac{J \cdot \omega_{m,N}^2}{P_{e,N}}

A electrical time constant for the armature circuit can be defined as:

.. math::

    \tau_e = \frac{L_a}{R_a}


.. _regenerative_braking_of_the_motor:

Regenerative braking of the motor
==================================

.. https://electronics.stackexchange.com/questions/56186/how-can-i-implement-regenerative-braking-of-a-dc-motor

It is relatively straight forward to apply control signals which converts the rotational energy of the brushed DC motor back to electrical energy. This is the most energy efficient way of breaking the motor, as the energy can be recovered and used again for another purpose. Regenerative operation on the arduino board can be problematic, as the USB power supply does not support reversed energy flow. The same is true if the supply comes from a battery which is not rechargeable. If one is pushing energy in to a DC source which does not support regenerative operation, the result will typically be that the voltage at the filtering capacitors in the DC section will increase. At some point the voltage will exceed the rating of the capacitors, and magic smoke will come out. Thus the first ting you have to do before considering regenerative operation, is to verify that the source will be able to handle it.

The trick to achieve regenerative braking it to increase the voltage coming from the motor to a level above the source voltage which was previously supplying the motor. This will allow the current direction to reverse, and thus the power flow will also be reversed. The voltage produced by the motor is proportional to the speed as well as on the magnetic field in the stator. It will always be lower than the source voltage, unless one of the following conditions are met:

- For a electrically excited DC motor, if the field current is increased.
- For any DC motor if some external mechanical force is driving the motor to a sufficiently high speed.

Unless you have a field winding (the small DC motor in the examples provided in this lesson does not) you will have to increase the induced voltage after it has been generated. In order to increase the motor voltage an additional external voltage converter (these are known as boost converters) could have been placed between the motor and the source, and activated when entering breaking mode. It turns out however that it is possible to exploit the internal inductance of the motor in order to increase (boost) the voltage.

If the transistors are switched correctly and at an appropriate switching frequency the only condition needed to obtain the regenerative braking is that the duty cycle of the PWM in reduced to a value below the previous steady state duty cycle. The following block diagram gives some insight in to the internal operation of the L293D:

    .. figure:: ../../../external/fig/l293d/l293d-detailed-block-diagram.png
            :align: center
            :alt: L238D detailed block diagram

    Source: `The L293 datasheet <https://www.ti.com/lit/ds/symlink/l293.pdf>`_

For each of the four drivers we have the following truth (function) table:

    .. figure:: ../../../external/fig/l293d/l293d-driver-truth-table.png
            :align: center
            :alt: L293D driver output truth table

    Source: `The L293 datasheet <https://www.ti.com/lit/ds/symlink/l293.pdf>`_

If the enable (EN) signal is low, the control signal (A) is ignored and the output goes in to a high impedance (high Z) state. When the enable signal is high, the output follows the control signal (A). By applying PWM to the enable signal and leaving the A signals fixed regenerative braking will be impossible. Whenever the output is in a high Z state, the motor current will have to circulate through the freewheeling diodes. If however the PWM signal is applied to e.g. 1A while 2A is low, a reduction in duty cycle will allow the voltage to be boosted by the motor inductance. For the opposite rotational direction the PWM signal should be applied to 2A while 1A is low.


Advanced motor drive example
=============================

In the following example a potentiometer is used to control the voltage level (i.e. speed) of the motor, one push button changes rotation direction, and the other turns the drive on and off. The main idea is pretty much the same as the first program. The code is a bit more sophisticated and safer to use. The control software is built around a hierarchical `State Machine <https://www.wikiwand.com/en/Finite-state_machine>`_, that simplifyes the management of the different states in which the motor drive may be operating. Put attention of the usage of :code:`typedef`.

.. figure:: ../../fig/dc_motor_drive_potmeter_control_bb.png
    :scale: 50
    :align: center


The following state diagram depicts the operation of the software:

.. Apparently Gizem decided to go for images instead of UML plugin last year. Let's change it back now :P

.. .. figure:: ../../fig/uml/motorctrlStateDiagram1.png
    :align: center


.. uml::

        [*] --> STOPPED
        
        STOPPED -> RUNNING : enable_button
        RUNNING -> STOPPED : enable_button
        STOPPED: motor_control(0, 0);
        
        RUNNING: duty_cycle = analogRead(control_voltage)/4;
        state RUNNING {
          [*] --> FORWARD
          FORWARD -> REVERSE : direction_button
          REVERSE -> FORWARD : direction_button

          FORWARD: motor_control(duty_cycle, FORWARD);
          REVERSE: motor_control(duty_cycle, REVERSE);
        }
        

The following source code listing is the complete software for the motor drive:

.. container:: toggle

    .. container:: header

        **Show/Hide Code**

    .. literalinclude:: ../../../projects/dc_motor_drive/src/main.cpp
       :language: c


Motor drive assignment
----------------------

The following circuit is the one used in assignment 5.

.. figure:: ../../fig/dc_motor_drive_potmeter_control_three_button_9v_battery_bb.png
  :scale: 50


The following state diagram depicts the intended operation of the motor drive in the assignment:

.. .. figure:: ../../fig/uml/motorctrlStateDiagram2.png
     :align: center


.. uml::

        STATE NORMAL {

          [*] --> STOPPED
          
          STOPPED -> ACCELERATING : start_stop_button
          ACCELERATING -> RUNNING : setpoint_reached
          ACCELERATING -> BREAKING : start_stop_button
          RUNNING -> BREAKING : start_stop_button
          BREAKING -> STOPPED : zero_reached
          BREAKING -> ACCELERATING : start_stop_button
          STOPPED: motor_control(0, 0);
          
        }

        [*] --> NORMAL

        NORMAL -> EMERGENCY_STOP : emergency_stop_button
        EMERGENCY_STOP -> NORMAL : start_stop_button
        
        EMERGENCY_STOP : motor_control(0, 0);