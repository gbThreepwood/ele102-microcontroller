:orphan:

.. _L3_Arduino_intro:

..
        .. note:: *10/02/2021*

            **Aim:**

            - Digital input/output
            - Analog input (no output)
            - How to deal with time in Arduino

            **Materials:**

            Arduino Board

            **Code:**

            Various sensor applications
            Timing related applications

*************************
Digital - Analog - Time
*************************

In this lesson we will have a more detailed look at how to deal with digital inputs, which was introduced in the previous lesson. Further on we will see how to read analog values, and how to deal with timing in the microcontroller.

..
        Everything in Arduino has to be digital. Arduino (or any uC/PC) cannot process analog data. It can receive analog, though. But can it produce analog? By the way, what is the time for a microcontroller?

Edge detection
=================

.. **Practical example:** Tilt Sensor (basic)
        *Probably won't work :)*

.. The reason why your program might not work now was because that you detected only one state but not the other one. The transitions between stated should be detected by the microcontrollers somehow. This problem is considered under the *Edge Detection* concept. 

Let’s think about what happens when you press a button. In this example (and the last) we have a digital pin connected to 5 volts through a tilt sensor. When the system is vertical, two pins are in contact. The 5 volts is applied to the digital pin. However, when you tilt the system, the connection is broken. Therefore, the system is not in *OFF state* but in an *unknown state*. Consider the figure below:

.. figure:: ../../../external/fig/switchFailure.png
   :align: center

.. ref: https://www.programmingelectronics.com/tutorial-18-state-change-detection-and-the-modulo-operator-old-version/

.. figure:: ../../../external/fig/edgedetection.jpg
   :align: center

   Source: `programmingelectronics.com <https://www.programmingelectronics.com/tutorial-18-state-change-detection-and-the-modulo-operator-old-version>`_


Debounce of mechanical switch
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. todo:: Add scope picture of the bouncing voltage on a mechanical switch

A mechanical switch will often generate spurious open/close transitions in a short period after it has been activated. It is a risk that these spurious transitions are interpreted as multiple signals from the switch. If your microcontroller appears to react strangely in the push button events you are providing, bouncing is the first cause you should expect.

In order to avoid these problems some form of debounce remedy should be applied. This could be a hardware solution, a software solution or a combination of the two.

If a software solution is desired, one possibility is to check the button state twice, within a short time window. I.e. check, delay, check again. We will be exploring some practical software solutions to this problem in a future lecture.

Hardware solutions include analog filters using resistors and capacitors, as well as using double throw switch and a SR-latch.

..
        It can be done by software, as well!

Rough Timing in Arduino
========================

The Arduino functions associated with timing that we will be using in this tutorial are:

- `delay() <https://www.arduino.cc/reference/en/language/functions/time/delay/>`_
- `delayMicroseconds() <https://www.arduino.cc/reference/en/language/functions/time/delaymicroseconds/>`_
- `millis() <https://www.arduino.cc/reference/en/language/functions/time/millis/>`_
- `micros() <https://www.arduino.cc/reference/en/language/functions/time/micros/>`_

In embedded systems it is often required to write code that has some delay between executing different parts of the code. For very simple applications it might be sufficient to use code that simply blocks until a given amount of time has passed. The `delay()` function is an example of such a function, it will block for the given number of milliseconds, before execution continues with the next line of instructions. For more sophisticated applications you might need the microcontroller to delay something, while still executing something else as fast as possible. Or you might have two operations which requires different delays. E.g. consider the simple example of blinking two LEDs at different rates. 

Operation
~~~~~~~~~~~

The Atmega328 has several built in hardware timers (two 8-bit and one 16-bit). These timers allow for many advanced timing applications. The Arduino library utilizes one of these timers for a counter that counts the number of microseconds since the last time the controller was restarted. Several functions are available for utilization of this counter value.

The `millis()` function returns the the number of milliseconds since the last time the controller was restarted. It is using a 32-bit unsigned integer to store the counter value, thus the maximum value is given by:

.. math::
        2^{32} = 4294967296

If we convert this value from milliseconds, to days we get:

.. math::
        \frac{4294967296}{1000 \cdot 60 \cdot 60 \cdot 24} = 49.7

Thus we see that the counter will wrap around after approximately 50 days. This should be accounted for in applications where the controller is running continuously for extended periods of time, but in many cases it will only cause a small glitch in the timing at the instant of wrap around.
        
If higher accuracy is required there is also a `micros()` function, returning the number of microseconds since the last reboot. Keep in mind that the maximum duration that the 32-bit register can store is significantly lower. Alternatively for maximum accuracy one of the unused hardware timers can be dedicated for your purpose.


Usage
~~~~~~~

We have already seen how the `delay()` function is used in the simple LED blink example. The `millis()` function allows us to do more interesting and useful delay implementations, where different parts of the code may execute while we are waiting for the required time to pass.


Exercise: Stopwatch
-------------------

In this exercise we will develop a stop watch.

.. figure:: ../../fig/digital_input_and_output_for_stop_watch_bb.png
        :alt: Digital input and output for stop watch
        :align: center
        :scale: 50

.. literalinclude:: ../../../projects/PseudoStopWatch/PseudoStopWatch.ino 
   :language: c

..
    Assignment 1 : Real time clock
    ---------------------------------

    .. code-block:: c
            
            void setup()
            {
            Serial.begin(9600);
            }
            
            int Year=2019;
            int Month=1;
            int Date=2;
            int Hour=12;
            int Minutes = 0;
            int Seconds = 0;
            
            void loop()
            {
            Seconds++;
            if (Seconds == 60)
            {
                Seconds = 0;
                Minutes++;
            }
            
            Serial.print(Hour);
            Serial.print(":");
            Serial.print(Minutes);
            Serial.print(":");
            Serial.println(Seconds);
            
            delay(1000);
            }

..
        Important timing functions in Arduino
        ----------------------------------------
        - `delay()` 
        - `delayMicroseconds()`
        - `millis()`
        - `pulseIn()`

        Each of these functions has a handy usage but timing is generally an issue in embedded world. We will see the bottleneck of these functions and how to deal with these problems in later lectures.

        Various Sensor Applications
        ---------------------------------
        **Note:** Vinly is a nice example for analog data storage.

        Question: What is sampling frequency? *(Details of analog is next week)*

        **Digital Sensors**
        - Button/Switch
        - Tilt
        - PIR
        - Ultrasonic Sensor(*)


        **Analog Sensors**
        - Potentiometer
        - Force/Torque Sensor
        - Temperature
        - Hummidity

        **Different Communications**
        - Inertial Measurement Unit
        - LCD
        - Bluetooth Module
        - Infrared Module
        - Wifi Module
        - Digial/Analog sensors on a module (mostly includes some filters)


        Outputs are either digital or different communication protocols:

        LED, vibrator, buzzer, motors, LCD, screen display.