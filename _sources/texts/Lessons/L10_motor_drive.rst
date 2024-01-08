.. _L8_motor_drive:

.. .. note:: *03/03/2020 - 75mins*

    **Aim:**

    - DC drive, H-bridge
    - Servo drive


    **Materials:**

    - Arduino Board
    - Button
    - Cables
    - Breadboard
    - Potentiometer
    - Resistors
    - Servo
    - DC

    **Code:**

    - Ex: Faster/Slower motor drive controlled with Potentiometer
    

**********************
Motor Drives
**********************

.. role:: ccode(code)
    :language: c

In general a motor drive is a power electronic device used to control the power delivered to a motor. The purpose could be either torque, speed or position control of the rotating motor shaft. At a high level the motor drive consists of a controller, and an amplifier (power electronics) which amplify the output from the controller to levels sufficient to drive the motor.

There are many different types of motors in existence, and each type warrants its own particular motor drive design. Unless the controller is very simple, microcontrollers are typically used for the controller, and the power electronics will be selected depending on type and size of the motor.

.. DC motor is the most common type of engine that can be used for many applications. (Sorry, Gizem, but I am not sure if this is correct.) 

**DC Motors:** This is a very common motor in cheap low power applications. We can see it in remote control cars, robots, etc. This motor has a simple structure. It will start rolling by applying proper voltage to its terminals and change it's rotational direction by switching voltage polarity. The DC motor torque (and thus speed) is directly controlled by the applied current. Current depends on voltage, and thus a voltage level less than the maximum tolerable voltage, will cause a speed that is less than maximum speed. By varying the applied voltage we will in practice wary the speed of the motor.

**Stepper Motors:** In some projects such as 3D printers, scanners and CNC machines we need to know motor spin steps accurately. In these cases, we may use stepper motors. Stepper motor are electric motors that divides a full rotation into a number of equal steps. The amount of rotation per step is determined by the motor structure. These kind of motors may be designed to have a very high accuracy if needed.

**Servo Motors:** There are many different types of motors that could be considered as servo motors. What they have in common it that they typically provide position control service. By using a servo you will be able to control the amount of shafts rotation and move it to a specific position. They usually have a small dimension and are the best choice for applications such as robotic arms.

**AC motors** Three phase AC motors are by far the largest consumer of electrical energy in the world. There are many different varieties of AC motors, but the type known as the induction motor serves a particularly important role in industry. This type of motor is typically used in applications where accuracy is less important, and for a very wide range of power levels from watts to megawatts. Examples include the driving of pumps, fans, drums, batching plants etc.

..  In order to control them since they possibly need more current than a microcontroller can drive so we need drivers. 

It is typically not possible to connect motors to microcontrollers or controller board such as Arduino directly. Unless the motor is really small it will require more current than the controller is able to supply. The Arduino UNO has a maximum drive capability of 40 mA on each pin. Thus we need some form of circuit to amplify the signal from the controller, this kind of circuit is called motor driver, or motor drive. The driver is an interface circuit between the motor and controlling unit to facilitate driving. Drives come in many different types :cite:`arduinoProjectHub`.

In this lecture we will look at DC-motor drives. The understanding of DC-motor drives is a good foundation for understanding more advanced AC-motor drives.

Introduction to DC motors
===============================

DC motors are divided into 2 main categories: *brushed* and *brushless* DC motors. We can define a brushed DC motor as a motor with internal mechanical commutation. It is designed to be powered by a direct current source. On the other hand, in the brushless DC motors, there is no physical contact between coils and the field magnet (stationary and rotaty parts). The brushless DC motor is really an AC motor with a electronic DC to AC converter placed inside the motor housing.

A brushless DC motor uses a permanent magnet as its external rotor and there are three phases of coils surrounding it. A specialized sensor is also typically placed in the setup to track the position of the rotor as it is moving, where the rotor position signals are being sent to a controller. A term often used for these devices is ESC (electronic speed controller). The ESC regulates the motor speed in order to track some reference speed, and can also provide dynamic braking where the rotational energy of the motor is converted back to electrical energy.

.. They are mostly used in fast-speed required applications like in drones since they have less corrision compared to brushed DC motors. Nevertheless, day by day their fields of usages are increasind. 

.. figure:: ../../../external/fig/Brushed-and-Brushless-DC-motors.jpg
          :align: center

          Source: `https://www.meee-services.com/why-do-dc-motors-have-brushes/ <https://www.meee-services.com/why-do-dc-motors-have-brushes/>`_


A brushed motor on the other hand, has a mechanical DC to AC converter (commutator), and the primary current is flowing in the rotor. As the rotor rotates so does the commutator, this causes the supply current to move to different coils and thus the direction of the generated magnetic field also moves.

..a configuration of wound wire coils, carrying out the duties of a two-pole electromagnet. The direction of the current is controlled by the commutator and this ensures the flow through the armature.

Brushed DC motors have been in commercial use since 1886. Brushless motors, on the other hand, did not become commercially viable until 1962. Brushed DC motors develop a maximum torque when stationary, linearly decreasing as velocity increases. Some limitations of brushed motors can be overcome by brushless motors; they include higher efficiency and a lower susceptibility to mechanical wear. These benefits come at the cost of potentially less rugged, more complex, and more expensive control electronics :cite:`motionControlGuide`.

.. Brushed DC motors on the other hand, is more widely used in the industry today. One should understand the working principle of a brushed DC motor before to go with brushless one.

.. figure:: ../../../external/fig/dcMotor.jpg
          :align: center

          Source: `https://www.motioncontrolonline.org/ <https://www.motioncontrolonline.org/blog-article.cfm/Brushed-DC-Motors-Vs-Brushless-DC-Motors/24>`_


.. figure:: ../../../external/fig/simpleDCinside.jpg
          :align: center

          Source: `https://itectec.com/ <https://itectec.com/electrical/electrical-self-inductance-in-steady-state-equivalent-model-of-brushed-dc-machine/>`_


.. figure:: ../../../external/fig/simpleDCinside2.jpg
          :align: center

          Source: `https://www.timotion.com <https://www.timotion.com/en/news-and-articles/part-2-components-of-an-electric-linear-actuator>`_




Brushed DC motor operation
---------------------------


A brushed DC-motor has a torque proportional to the current passing through the armature of the motor :cite:`mohanElectricMachinesDrives2012`.

.. math::
    T_{em} = (2 B_f \ell_r ) i_a

If the magnetic field of the stator is constant, the only variable in the torque equation is the armature current. Hence we can write:

.. math::
    T_{em} = k_T \cdot I_a

Where :math:`k_T` is the torque constant for the motor, and :math:`I_a` is the armature current.

As the windings of the rotor interact with the stator magnetic field, an electromotive force (EMF) is produced. This is a voltage and is proportional to the angular velocity (speed) of the rotor:

.. math::
    e = k_e \cdot \omega_m

Where :math:`k_e` is the EMF constant for the motor, and :math:`\omega_m` is the mechanical angular velocity. Based upon Newtons second law the following equation can be derived:

.. math::

    J \frac{\mathrm{d}\omega_m}{\mathrm{d}t} + B \omega_m = k_T \cdot i_a - T_L

Where :math:`J` is the moment of inertia, :math:`B` is the coefficient of viscous friction, and :math:`T_L` is the load torque.

Futhermore based on Kirchhoffs voltage law the following electrical equation is derived:

.. math::

    L \frac{\mathrm{d}i_a}{\mathrm{d}t} + R i_a = V_a - K_e \omega_m


If we use SI units, the torque constant, and the EMF constant has the exact same numerical value:

.. math::
    k_T = k_e = k


.. note:: An understanding of the transfer function is not required for the exercises in this lesson, but is important for closed loop control of the DC-motor. It is included in :ref:`transfer_function` for completeness.


Brushed DC-motor drive
======================

In this section we will cover how to control a brushed DC-motor by the Arduino UNO.

Control loops
-------------

From a mechanical point of view we have several possible control objectives:

* Torque control (current control, as mechanical torque is proportional to current)
* Speed control and control of rotational direction
* Position control 

The three control objectives in the above list are obviously related to each other. The change in position depend on the speed, and the change in speed depend on the torque. Hence we will often find that a cascaded control structure is used, e.g. the reference input to the torque controller could be the output from the speed controller. Accurate control required feedback, this could be in the form of a current sensor measuring the armature current, and a mechanical speed or position sensor on the motor shaft.

If you simply apply a voltage to the motor armature this will result is some current, which in turn causes torque and rotation of the motor. This is called open loop control since you not feeding back any of the resulting motor states, i.e. the control loop is not closed.. If you are not measuring anything it is hard to tell what the exact operating conditions (e.g. the speed of the motor) will be, but some previous knowledge about the system can allow you to estimate. E.g. by previous measurements you might know that in a given load situation applying 12 V will cause a speed of approximately 1000 RPM.

Closed loop control requires additional sensors, and knowledge of control theory. Thus In this lesson we will only consider open loop control, this is the foundation upon which closed loop control can be built in a future lecture.

.. TODO: Picture of encoder, and better picture of current sensor

.. image:: ../../../external/fig/sensors/current_sensor.avif
    :align: center


.. thus in this lecture, we will only consider open loop control. 

.. If we simply apply a voltage to the motor without making any measurements to determine the optimal voltage magnitude for a given performance criteria, this is referred to as open loop control.

.. By measuring the armature current, the speed, or the position of the rotor shaft, it is possible to implement closed loop control. 



.. We can divide controlling a DC motor concept in 2 chapters: Speed control and direction control.

Motor Speed Adjusting by PWM
--------------------------------

The most common way to vary the voltage level (and for AC motors also the frequency) in modern motor drives is by using pulse width modulation. A basic understanding of this concept is essential for anyone designing such drives.

.. note::
    As this course is a basic course we will not have time to go into all the details of how the PWM operates.

The Atmega 328 supports two major types of PWM:

* Fast PWM
* Phase correct PWM

Fast PWM mode
~~~~~~~~~~~~~~~

The frequency of the fast PWM mode is given by:

..
    .. math::
        f_{pwm} = \frac{\text{clock_speed}}{ \text{prescaler} \cdot (1 + \text{top_value})} 

Where clock_speed is the speed of the Arduino CPU clock (16 MHz by default). The prescaler is a number that is scaling the clock, and top_value is the maximum value of the counter.

Phase correct PWM
~~~~~~~~~~~~~~~~~~~~

The phase correct PWM is a mode where the phase of the PWM signal remains constant. This is important in some applications, such as AC motor drives.

..
    .. math::
        f_{pwm} = \frac{\text{clock_speed}}{2 \cdot \text{prescaler} \cdot \text{top_value}} 

Available outputs
~~~~~~~~~~~~~~~~~~~

The outputs that support PWM are labeled with a small sine wave symbol (actually a tilde  "~") on the Arduino board. The following table lists some important information regarding the PWM outputs. The `switching frequency` is only the default value. It may be changed by direct register manipulation, but the Arduino library does not have any functions to change it.

========== ========= ===========
Pin number Timer     Switching frequency
3           2           490 Hz
5           0           980 Hz
6           0           980 Hz
9           1           490 Hz
10          1           490 Hz
11          2           490 Hz
========== ========= ===========


Four quadrant operation
~~~~~~~~~~~~~~~~~~~~~~~~~~

The rotating shaft of an electric machine has two fundamental parameters, torque and speed. The speed may be forward or reverse, and the torque may be motoring or braking. Thus we have four possible modes of operation, i.e. four quadrant operation.


.. figure:: ../../../external/fig/four-quadrant-operatio-fig-compressor.jpg
          :align: center

          Source: `https://circuitglobe.com/four-quadrant-operation-of-dc-motor.html <https://circuitglobe.com/four-quadrant-operation-of-dc-motor.html>`_

For a brushed DC-motor it is possible to operate in all four quadrants by controlling the voltage applied to the armature. The motor drive must have the ability to control both the magnitude, and polarity of the voltage, and the circuit must allow the current to flow in both directions.

When the drive is operating in a mode with negative torque with respect to the rotating direction it is said to be in breaking (generator) mode. In this mode the energy is flowing from the motor back to the power source.

.. seealso::
   * How a brushed DC works: `BDC principle <https://www.youtube.com/watch?v=LAtPHANEfQo>`_
   * How a brushless DC works: `BLDC principle <https://www.youtube.com/watch?v=bCEiOnuODac>`_
   * Practical demonstration of the four quadrant operation: `four quadrant <https://www.youtube.com/watch?v=51yZrXBlCQY>`_
   * Speed-Torque relation in efficiency: `Motor efficiency map of a DC motor <https://www.semanticscholar.org/paper/Improved-torque-and-efficiency-of-a-four-switch-fed-Ekong-Inamori/60a9c55e2cead93ec4f68dc99f93be5647d07c88>`_


..Motor Direction Setting by Switching
.. -------------------------------------

.. The most efficient way to control the voltage level to the motor is by using some form of on/off modulation, commonly we use pulse width modulation. The digital outputs of the arduino has a maximum current output of 40mA. This current is far below what is required for most motor applications, and thus we need amplification.
.. 
.. The digital outputs may be used to drive transistors that are able to support the higher currents required for the motor. Because this is such a common use for transistors, it is possible to buy integrated circuits where several transistors are connected in a configuration for motor drive. 
.. 

Simple motor drive example using single transistor
----------------------------------------------------

..     This is an additional part just to see how to use a regular MOSFET to drive a DC motor.
   

In this example we will be using a `IFR520N` transistor to drive the small DC-motor that comes as part of the Arduino starter kit. The `IRF520N datasheet <https://www.vishay.com/docs/91017/91017.pdf>`_ provides the required details on the electrical limitations of the transistor, but for this example you should only trust us in that it will operate within limits.

The function :ccode:`analogWrite()` will be used to generate a PWM signal to the transistor, and the switching frequency will stay at it's default value.


.. figure:: ../../fig/dc_motor_drive_single_transistor_bb.png
    :scale: 50
    :align: center


Exercise: start and stop of motor
---------------------------------

#. Do the wiring according to the previous drawing
#. Add code to properly read, edge detect, and debounce a push button
#. Use the rising edge of the push button to toggle the motor driver on, or off. The duty cycle should be 50%.
#. Read a parameter from the UART, which is used to set the duty cycle. The parameter should be a number between 0, and 100. Anything else should produce an error message.

.. literalinclude:: ../../../projects/dc_motor_drive_single_transistor/src/main.cpp
    :language: c


LM293D H-Brigde
-----------------------------------

An H bridge is an electronic circuit that switches the polarity of a voltage applied to a load. These circuits are often used in robotics and other applications to allow DC motors to run forwards or backwards :cite:`hbridge`.

.. L238D is a double H-bridge IC.  `Here is the datasheet <https://cdn-shop.adafruit.com/datasheets/l293d.pdf>`_.
.. Let's set the following circuit and upload the code deprived from `https://howtomechatronics.com/ <https://howtomechatronics.com/tutorials/arduino/arduino-dc-motor-control-tutorial-l298n-pwm-h-bridge/>`_ and talk about the concept behind.

This section provides a basic overview of the LM293D driver. More details about are available in the `datasheet <https://www.st.com/resource/en/datasheet/l293d.pdf>`_

.. image:: ../../fig/l293d_package.png
    :align: center

The L293D is a quadruple high-current half-H driver. The D in the name signifies that this version incorporates diodes on the outputs. It has a maximum output current of 600 mA, a maximum switching frequency of 5 kHz, and supports a voltage range from 4.5 to 36 V.


.. image:: ../../fig/l293d_logic_diagram.png
    :align: center

Depending on the degree of control that your application requires there are several possible ways to connect the motor.


.. image:: ../../fig/l293d_motor_connection_examples.png
    :align: center

The following tables provides an overview of the possible control signal inputs, and the effect they will have in a DC motor drive application.

.. figure:: ../../../external/fig/l293d/table-3-part-1.png
          :align: center

          Source: `The L293 datasheet <https://www.ti.com/lit/ds/symlink/l293.pdf>`_

.. figure:: ../../../external/fig/l293d/table-3-part-2.png
          :align: center


..          Source: `The L293 datasheet <https://www.ti.com/lit/ds/symlink/l293.pdf>`_




A very basic example for the LM293D
-----------------------------------

The following code listing provides a very basic example on how to set a specific operating mode for the LM293D motor driver. Two digital outputs on pin 4, and 5 are used to set the rotational direction for the motor by setting the voltage polarity. A third digital output on pin 6 is used to provide a PWM signal to the enable input which causes the voltage applied to the motor to switch on and off quickly.

The motor supply voltage pin is connected to the 5 V output from the Arduino. This is fine for a small motor, but larger motors with high current or voltage rating will require an external supply to this pin.

.. literalinclude:: ../../../projects/platformio/motor-drive/basic-dc-motor-drive-l293d/src/main.cpp
    :language: c

.. note:: Applying PWM to the 1A, and 2A pins will also cause the output voltage to be modulated. This will not have the exact same effect though. The difference is outlined in advanced topics :ref:`regenerative_braking_of_the_motor`.

.. It is no problem to test this, but on should pay some attention to the temperature of the integrated circuit, just to make sure that it does not increase the dissipation.
.. On some motor drivers is is required to add som delay between turning off one control signal and turning on the next.

.. Motor drive using the L293D
.. ---------------------------
.. 
.. In order to fully control a motor using the L293D you need three digital signals. Two signals to `IN1`, and `IN2` for selection of the rotation direction, and one PWM signal to `ENABLE1` for speed control.
.. 
.. In the following example we have one potentiometer to control the speed of the motor and one push button to toggle the direction.
.. 
.. .. figure:: ../../../external/fig/DC_speed_control.png
..           :align: center
.. 
.. 

.. .. literalinclude:: ../../../projects/DC_speed_control/DC_speed_control.ino
..    :language: c

Control function for easy use of the L293D
------------------------------------------------

In order to easily use the L293D, and also to easily change to a different motor driver without having to make a lot of changes to your code, it is a good idea to abstract away the low level details in a function. The function should take parameters for the duty cycle, as well as for the voltage polarity which determine the rotational direction.

The following code listing shows an example of such a function, which support the four most basic functions that is available for DC-motor driving using the LM293D.

.. code-block:: c

    typedef enum _drive_ctrl_t {
        DIR_FORWARD = 0,
        DIR_REVERSE,
        CTRL_STOP,
        CTRL_EM_STOP
    } drive_ctrl_t;

    void motor_drive_control(uint8_t duty_cycle, drive_ctrl_t ctrl){

      switch (ctrl)
      {
      case DIR_FORWARD:
        digitalWrite(a1_pin, HIGH);
        digitalWrite(a2_pin, LOW);
        analogWrite(en_pin, duty_cycle);
        break;

      case DIR_REVERSE:
        digitalWrite(a1_pin, LOW);
        digitalWrite(a2_pin, HIGH);
        analogWrite(en_pin, duty_cycle);
        break;

      case CTRL_STOP:
        digitalWrite(a1_pin, LOW);
        digitalWrite(a2_pin, LOW);
        digitalWrite(en_pin, LOW);
        break;

      case CTRL_EM_STOP: 
        digitalWrite(a1_pin, LOW);
        digitalWrite(a2_pin, LOW);
        digitalWrite(en_pin, HIGH);
        break;

      default:
        break;
      }
    }

The listing starts by defining a :code:`enum`, which provides names for the four possible values to the second parameter of the function. This is not mandatory but helps in improving the readability of the code, as well as providing some type safety. The four parameters are translated to numbers, but numeric values outside the range will cause compiler warnings.

Depending on the received parameter, the :code:`switch` will set the appropriate logic levels to the output pins, and also set the duty cycle if needed.

Exercise-1: Control motor speed with a potentiometer
------------------------------------------------------

Set the circuit below. The button is not required for this step (yet). And start thinking how to control the motor speed via potentiometer :)

.. figure:: ../../../external/fig/dc_motor_drive_circuit.png
         :align: center


Exercise-2: Change rotation direction using a button
-------------------------------------------------------

Use the same circuit above, modify your code such that the motor change rotation direction everytime you press the button.



A slightly more complete case with start, stop, and emergency stop
---------------------------------------------------------------------

The emergency stop is implemented by forcing the motor to stop as quickly as possible. By setting both 1A, and 2A low (or high) simultaneously while keeping the EN signal high the motor terminals will be short circuited to each other. This will cause a large current to flow in the motor windings, and the rotational energy will be dissipated as heat in the winding resistance.

.. literalinclude:: ../../../projects/platformio/motor-drive/em-stop-dc-motor-drive-l293d/src/main.cpp
    :language: c

Start/stop, forward/reverse and speed control
---------------------------------------------

The previous example only supported operation of the motor in one direction. In addition to the forward/reverse commands speed control should also be supported by means of adjustable duty cycle. For the upcoming exercises you should use the following wiring:

.. The following example is a slightly more complete implementation of motor control using the L293D driver. The implementation uses one push button for starting, and one for stopping the motor. The voltage on analog input `A0` is used to control the duty cycle of the pulse width modulation.

.. figure:: ../../fig/dc_motor_drive_potmeter_control_bb.png
    :scale: 50
    :align: center


..  .. literalinclude:: ../../../projects/motor_drive/dc_motor_drive/src/main.cpp
..    :language: c

Exercise: LM293D motor drive
-----------------------------

#. Use the L293D motor driver and perform the necessary wiring. There should be 3 push buttons and a potentiometer.
#. Write an application which takes the armature voltage (motor speed) set point as a input from a potentiometer.
#. Read the signals from three push buttons with proper debouncing, and edge detection.
#. Add the code to use the first push button to toggle between start and stop.
#. Add the code to use the second push button to toggle the rotational direction. The program should only allow the direction to change if the motor is first stopped.
#. Add the code for emergency stop on the third button. If pressed the program should go in to emergency stop mode, and stay there until the controller receives the command "reset", or "RESET" from the UART.
#. Test the program, and write a short description about the behavior of your code.

Exercise: motor drive with acceleration ramp
--------------------------------------------

In this exercise you are going to develop a motor drive which has an adjustable acceleration, and deceleration ramp. I.e. when the motor is started there will be a acceleration period where the armature voltage is increased linearly from zero, and up to the configured voltage.

#. Use the program you have developed in the previous exercise as a starting point. Make sure everything in that exercise is working as expected
#. Add a routine to slowly accelerate the motor from standstill and up to the potentiometer setting. A parameter (const variable) should set the acceleration time, and it should support at least 0 to 20 seconds.
#. Add a routine to slowly decelerate the motor from nominal operation and down to standstill. If the stop button is pressed while the motor is accelerating, the deceleration routine should take precedence and use the correct portion of the configured deceleration delay down to standstill.

Regenerative braking of the motor
---------------------------------

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

Simple motor drive example using the L293D
------------------------------------------------

Here is the simplest example with L298D motor driver and one single DC motor.

    .. figure:: ../../../external/fig/l298d-simple.png
            :align: center
            :alt: simple L293D 

    
The simplest code for such a setup can be:

.. code-block:: c

    const uint8_t in1 = 9;
    const uint8_t in2 = 10;
    const uint8_t en = 11;

    void setup()
    {
    pinMode(in1, OUTPUT);
    pinMode(in2, OUTPUT);
    pinMode(en, OUTPUT);
    
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    analogWrite(en, 127);
    
    Serial.begin(9600);
    }

    void loop()
    {
        delay(1000);
    }

        
As a next step, we can create some functions to simplify the motor control commands:

.. code-block:: c

    const uint8_t in1 = 9;
    const uint8_t in2 = 10;
    const uint8_t en = 11;

    void setup()
    {
    pinMode(in1, OUTPUT);
    pinMode(in2, OUTPUT);
    pinMode(en, OUTPUT);
    
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    analogWrite(en, 127);
    
    Serial.begin(9600);
    }

    void loop()
    {
    rotate_right();  
    }

    void rotate_right(){
    
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    analogWrite(en, 127);
    
    }

    void rotate_left(){
    
    digitalWrite(in1, HIGH);
    digitalWrite(in2, LOW);
    analogWrite(en, 127);
    
    }


Also, the motor speed can be defined as a function input:

.. code-block:: c

    const uint8_t in1 = 9;
    const uint8_t in2 = 10;
    const uint8_t en = 11;

    void setup()
    {
    pinMode(in1, OUTPUT);
    pinMode(in2, OUTPUT);
    pinMode(en, OUTPUT);
    
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    analogWrite(en, 127);
    
    Serial.begin(9600);
    }

    void loop()
    {
    int speed_1 = 255;
    int speed_2 = 127;
    rotate_right(speed_1);
    delay(2000);
    rotate_left(speed_2);
    delay(2000);
    
    }

    void rotate_right(int speed){
    
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    analogWrite(en, speed);
    
    }

    void rotate_left(int speed){
    
    digitalWrite(in1, HIGH);
    digitalWrite(in2, LOW);
    analogWrite(en, speed);
    
    }


Feature rich motor drive example using the L293D
------------------------------------------------

The following example is a slightly more complete implementation of motor control using the L293D driver. The implementation uses one push button for starting, and one for stopping the motor. The voltage on analog input `A0` is used to control the duty cycle of the pulse width modulation.

.. note:: You will see the :code::`typedef` usage in the coming code. It is a beautiful way of creating new object types in C-based languages. Although this keyword is not within the scope of this course, it is still an important/nice feature to know the usage. The rest of the code is easy to follow, nonetheless.

.. figure:: ../../fig/dc_motor_drive_potmeter_control_bb.png
    :scale: 50
    :align: center


.. literalinclude:: ../../../projects/motor_drive/dc_motor_drive/src/main.cpp
  :language: c

Exercise: LM293D motor drive
-----------------------------

#. Use the L293D motor driver and perform the necessary wiring. There should be 3 push buttons and a potentiometer.
#. Write an application which takes the armature voltage (motor speed) set point as a input from a potentiometer.
#. Read the signals from three push buttons with proper debouncing, and edge detection.
#. Add the code to use the first push button to toggle between start and stop.
#. Add the code to use the second push button to toggle the rotational direction. The program should only allow the direction to change if the motor is first stopped.
#. Add the code for emergency stop on the third button. If pressed the program should go in to emergency stop mode, and stay there until the controller receives the command "reset", or "RESET" from the UART.
#. Test the program, and write a short description about the behavior of your code.

Exercise: motor drive with acceleration ramp
--------------------------------------------

In this exercise you are going to develop a motor drive which has an adjustable acceleration, and deceleration ramp. I.e. when the motor is started there will be a acceleration period where the armature voltage is increased linearly from zero, and up to the configured voltage.

#. Use the program you have developed in the previous exercise as a starting point. Make sure everything in that exercise is working as expected
#. Add a routine to slowly accelerate the motor from standstill and up to the potentiometer setting. A parameter (const variable) should set the acceleration time, and it should support at least 0 to 20 seconds.
#. Add a routine to slowly decelerate the motor from nominal operation and down to standstill. If the stop button is pressed while the motor is accelerating, the deceleration routine should take precedence and use the correct portion of the configured deceleration delay down to standstill.


           

Bibliography
============

.. bibliography:: ../../bib/motor_drives.bib 
   :style: unsrt
