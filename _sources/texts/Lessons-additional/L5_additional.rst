*******************************************************
Additional material for lesson 5
*******************************************************


Detailed look at the Atmega328p ADC
===================================        

In the ADC hardware of the Atmega328p, there are many different parameters that needs to be configured properly. For example setting the ADC speed, resolution, conversion mode etc. By using the :code:`analogRead(analogPin)` Arduino function without changing anything, we simply accept the parameters that the Arduino developers hav selected for us. The default settings are fine for basic usage, but there are many situations where you would have to go in to the details.


Voltage reference for the ADC
-----------------------------

As discussed previously, the analog to digital converter uses a voltage reference for comparison with the analog signal it is converting. The Atmega328p microcontroller has several options for where to obtain this reference. In the Arduino UNO library these reference options may be set by the :code:`analogReference()` function, which takes the parameters `DEFAULT`, `INTERNAL`, or `EXTERNAL`, for 5V, 1.1V or externally applied voltage to the AREF-pin respectively. The selected reference voltage will be the maximum input voltage for the ADC, i.e. the voltage that will correspond to the digital value of 1023.

.. code-block:: c

        analogReference(EXTERNAL);

If you are only interested in measuring voltages from 0 - 2 V, a external reference of 2 V will provide better resolution than the default reference of 5 V.

The accuracy of the reference voltage directly affects the accuracy of the converted voltage, and this is another motive for using an external reference. By accuracy in this context we mean: how sure can you be that the voltage actually is 5 V, is it 5.00 V, or could it be 5.02 V? A highly precise reference voltage can be used if high accuracy is required.

..
    Light intensity measurements using a phototransistor
    ----------------------------------------------------

    https://www.arduino.cc/documents/datasheets/HW5P-1.pdf


    Voltage reference experiment using potentiometer
    ------------------------------------------------

    In this example we will be using two potentiometers, one for adjusting the voltage reference, and one for adjusting the input voltage to the ADC.



ADC conversion speed
--------------------

The conversion speed is determined by the clock signal to the successive approximation register in the ADC. There is a maximum limit to this clock, after which the ADC starts misbehaving. The recommended maximum clock speed is 200 kHz, while the specified absolute maximum speed is 1 Mhz.

In the Arduino library the clock is derived from the CPU clock by using a prescaler of 128

.. math::
	\frac{16 Mhz}{128} = 125 kHz

Each conversion takes 13 ADC clock cycles, which yields a sample rate of:

.. math::
	\frac{125 kHz}{13} = 9615 Hz

The conversion time is thus given by:

.. math::
	\frac{1}{9615} = 104 us

This corresponds well with the conversion time stated in the documentation for the :code:`analogRead()` function in the Arduino library.

Low level control of the Analog to Digital Conversion
-----------------------------------------------------

Here is an example of the implementation of ADC without using built-in :code:`analogRead(analogPin)` function. There are different ways of bitwise operations. It is better to be consistent in using one way but here we would like to see different ways. Let's understand it with the help of Atmega328p datasheet.

.. literalinclude:: ../../../projects/ADC_full/ADC_full.ino
   :language: c


.. note::

   ADC is a demanding topic also requires basic signal processing knowledge. One should read about possible errors before computing a serious analog to digital conversion. `Here, <https://www.maximintegrated.com/en/design/technical-documents/tutorials/6/641.html>`_ is a very nice summary of general ADC problems. 

.. The ATmega controllers used for the Arduino contain an onboard 6 channel (8 channels on the Mini and Nano, 16 on the Mega) analog-to-digital (A/D) converter. The converter has 10 bit resolution, returning integers from 0 to 1023. The default input voltage range is 0 - 5V, thus 5V corresponds to 1023. By default the maximum sampling frequency of the Arduino UNO is 9615 Hz. It is however possible to increase this by modifying the ADC clock frequency.

While the main function of the analog pins for most Arduino users is to read analog sensors, the analog pins also have all the functionality of general purpose input/output (GPIO) pins (the same as digital pins 0 - 13). Consequently, if a user needs more general purpose input output pins, and all the analog pins are not in use, the analog pins may be used for GPIO.


.. warning:: 
   The analogRead function will not work correctly if a pin has been previously set to an output, so if this is the case, set it back to an input before using analogRead. Similarly if the pin has been set to HIGH as an output, the pull-up resistor will be set, when switched back to an input.
   
   The ATmega datasheet also cautions against switching analog pins in close temporal proximity to making A/D readings (analogRead) on other analog pins. This can cause electrical noise and introduce jitter in the analog system. It may be desirable, after manipulating analog pins (in digital mode), to add a short delay before using analogRead() to read other analog pins.


Increasing resolution by oversampling
-------------------------------------

.. todo:: Explain how to use oversampling (and why it works)



Servo Motor Drive
=================

There is no principal difference between a servo motor and any other kind of motor. The difference lies in how one intends to use it. A servo motor is intended for precise control of angular or linear position, and thus it typically employs a position sensor on the shaft. Traditionally brushed DC-motors have been the preferred choice for servo motors, due to the ease of control. Today however there are many different classes of motors that are used for servo application.

The Arduino starter kit contains a special servo motor `SM-S2309S` which has some features that are quite usefull for many servo applications. It has a built in gearbox that reduces the speed and increases the torque on the shaft, and a built in position sensor, wich allows us to simply request a potion, and the motor will move to that position.

The following `video <https://www.youtube.com/watch?v=LXURLvga8bQ>`_ gives a good explanation to the internal operation of this kind of servo motor.

.. .. youtube:: https://www.youtube.com/watch?v=LXURLvga8bQ 


Switching frequency
-------------------

The `analogWrite()` function in the standard Arduino library only support one parameter, specifying the duty cycle of the generated PWM output. Another important parameter of PWM however is the frequency at wich the pulses are turned on and off. The Arduino library does not support changing this frequency, but it is possible to change it by accessing the registers of the timers responsible for generating the PWM.

.. warning::
        Changing the registers may impact the operation of other features of the Arduino library, such as the :ccode:`millis()` function.

The various hardware timers in the microcontroller are responsible for various groups of PWM outputs. Thus by manipulating a given hardware timer, you will be manipulating the frequency of some of the PWM outputs.



.. code-block:: c

        pinMode(3, OUTPUT);
        pinMode(11, OUTPUT);
        TCCR2A = _BV(COM2A1) | _BV(COM2B1) | _BV(WGM21) | _BV(WGM20);
        TCCR2B = _BV(CS22);
        OCR2A = 180;
        OCR2B = 50;


Servo motor library
-------------------

Servo motors typically requires 20ms period of PWM signals (50Hz) with pulse width varying between 1ms-2ms (0-180 degrees) as a driving signal. Since the generic :code:`analogWrite()` works in 490Hz/ 980Hz, you cannot drive a servo motor unless you fiddle around the timer settings. Luckily, Arduino comes with a servo library that simplifies the control of the servo motor.

The following example shows how you may send commands on the serial UART to control the servo position. Given 5 different values via serial port, control the servo angle.

.. literalinclude:: ../../../projects/ServoSerial/ServoSerial.ino
    :language: c



We talked about that we drive servo motors using PWM signal but also we didn't use the regular :code:`analogWrite()` function. It wouldn't work. Let's see the difference of the signal types between :code:`analogWrite()` and :code:`myservo.write()` using a `logic analyzer <https://www.wikiwand.com/en/Logic_analyzer>`_.

.. figure:: ../../../external/fig/logicAnalogWrite.png
          :align: center
          
          Logic Analyzer result for analogWrite() function

.. figure:: ../../../external/fig/logicServoWrite.png
          :align: center

          Logic Analyzer result for myservo.write() function


Servo motor drive example
-------------------------

The following example uses a push button to toggle through various positions on the servo motor.

.. literalinclude:: ../../../projects/motor_drive/servo_motor_control/src/main.cpp
  :language: c

