:orphan:

.. _L7_adc_pwm_interrupt:

.. note:: *26/02/2020 - 195mins*

    **Aim:**

    - cont’d prev lesson using interrupts. 
    - Debouncing problem. 
    - Priorities
    - read pwm – compare action based fuctions, why pulsein not good and we use interrupts, relate with edge detection

    - remember there is no analog out in arduino.
    - pwm is the easiest was to trick it
    - timer concept cont'd
    - Motor drive with PWM using interrupt
    - joysick read via pwm
    - arduino pwm specs.
    - servo lib pwm requests
    - pwm in different Arduino boards

    **Aditional***
    - extern, global, volatile, const vs #define
    - variable types
    - get Data, put Data
    - memory is important because it is a 'micro'controller
    - Servo, 
    - Analog read, 
    - Time, 
    - pulse in – Servo move with pot (?)
    - introduce arrays


    **Materials:**

    - Arduino Board
    - Button
    - Cables
    - Breadboard
    - Potentiometer
    - Resistors
    - Servo

    **Code:**

    - Ex: interrupt via button. 
    - Hw: starts stops counting in 2 timers. print it out via serial. Recall: izel ödev


*****************************************
ADC PWM Interrupts
*****************************************


Analog to Digital Conversion (ADC)
====================================

Analog to digital converters (ADC) and digital to analog converters (DAC) are
the bridges between digital and analog worlds. The analog input or output range is
determined by a reference voltage, :math:`V_{ref}`. Typically for an N-bit converter with
unsigned digital I/O and unipolar analog range :math:`(0V .. +V_{ref})`, one step at the analog
end, :math:`\Delta V_{LSB}`, is given by:

.. math::
    \Delta V_{LSB} = \frac{V_{ref}}{2^N}

Similarly for a bipolar analog range :math:`(-V_{ref} .. +V_{ref})`, one step at the analog end is:

.. math::
    \Delta V_{LSB} = \frac{V_{ref}}{2^N}


ADC: potentiometer, RGB LED, temperature sensor
DAC: Quantization, PWM

Analog to digital converter
---------------------------
A A/D converter (analog to digital converter) is a device that converts a analog signal into an approximate digital representation. There are many important parameters one should understand when applying an A/D converter. This is only a summary of a few basic ones:

* Resolution - The digital representation of the analog signal must use a finite number of bits, and this imposes limitations on the smallest possible change in the analog value that is detectable by the converter.
* Sampling frequency - The conversion from analog to digital takes up a finite time, and this imposes limitation on how fast changes in the analog signals that are detectable.
* `Aliasing <https://jeelabs.org/article/1620b>`_ - If the measured analog signal contains components with a frequency that is higher than half of the sampling frequency, there is a risk of aliasing. The risk is that the resulting digital representation contains frequency components that does not exist in the real analog signal.
* Dymaic range -  The range of signal amplitudes that the ADC can resolve.

For simple applications with values that vary slowly (e.g. temperature measurements), it might be sufficient to only take these parameters into account. For more demanding applications (e.g. real time current measurements in a motor drive), one should obtain a deeper knowledge of all the parameters that will impact the performance. 

.. ref: https://web.ics.purdue.edu/~jricha14/Port_Stuff/PortA_ADC.htm

But before that, let's talk about bit manipulation and binary operations. Detailed readinng `here <https://binaryupdates.com/bitwise-operations-in-embedded-programming/#Bitwise_Shift_Operators_ltlt_gtgt>`_.

.. note::
   Blackboard explanation.


In ADC, there are many different parameters that you need to set. Using :code:`analogRead(analogPin)` built-in function, we cannot reach many of the functionalities of Atmega328p. For example setting the ADC speed, resolution, convertion mode etc. can be only set by bitwise manipulation.

Here is an example of the implementation of ADC without using built-in :code:`analogRead(analogPin)` function. There are different ways of bitwise operations. It is better to be consistent in using one way but here we would like to see different ways. Let's understand it with the help of Atmega328p datasheet.

.. literalinclude:: ../../../projects/ADC_full/ADC_full.ino
   :language: c


.. note::

   ADC is a demanding topic also requires basic signal processing knowledge. One should read about possible errors before computing a serious analog to digital conversion. `Here, <https://www.maximintegrated.com/en/design/technical-documents/tutorials/6/641.html>`_ is a very nice summary of general ADC problems. 

The ATmega controllers used for the Arduino contain an onboard 6 channel (8 channels on the Mini and Nano, 16 on the Mega) analog-to-digital (A/D) converter. The converter has 10 bit resolution, returning integers from 0 to 1023. The default input voltage range is 0 - 5V, thus 5V corresponds to 1023. By default the maximum sampling frequency of the Arduino UNO is 9615 Hz. It is however possible to increase this by modifying the ADC clock frequency.

While the main function of the analog pins for most Arduino users is to read analog sensors, the analog pins also have all the functionality of general purpose input/output (GPIO) pins (the same as digital pins 0 - 13).

Consequently, if a user needs more general purpose input output pins, and all the analog pins are not in use, the analog pins may be used for GPIO.


.. warning:: 
   The analogRead function will not work correctly if a pin has been previously set to an output, so if this is the case, set it back to an input before using analogRead. Similarly if the pin has been set to HIGH as an output, the pull-up resistor will be set, when switched back to an input.
   
   The ATmega datasheet also cautions against switching analog pins in close temporal proximity to making A/D readings (analogRead) on other analog pins. This can cause electrical noise and introduce jitter in the analog system. It may be desirable, after manipulating analog pins (in digital mode), to add a short delay before using analogRead() to read other analog pins.


Temperature measurement using TMP36
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now, we will be reading the temperature from the TMP36 sensor that is included in the Arduino kit. The TMP36 has a voltage output linearly proportional to the temperature, and thus makes it easy to measure temperatures without any curve fitting that must be used with nonlinear sensing elements.


The details of how the TMP36 operates are available in the `datasheet <https://www.analog.com/media/en/technical-documentation/data-sheets/TMP35_36_37.pdf/>`_

  
.. figure:: ../../fig/temperature_sensor_bb.png
        :align: center
        :scale: 50


.. code-block:: c
                
        #include <Arduino.h>
        
        void setup() { 
                Serial.begin(9600); 
        }
        
        void loop() {
       
                  int sensorVal = analogRead(0);
                  float sensorVolt = (sensorVal/1024.0)*5;
                  float temperature = (sensorVolt - 0.5)*100;
                
                  Serial.print("Sensor verdi: ");
                  Serial.print(sensorVal);
                  Serial.print("\n");
                
                  Serial.print("Sensor spenning: ");
                  Serial.print(sensorVolt);
                  Serial.print("\n");
                
                  Serial.print("Sensor temperatur: ");
                  Serial.print(temperature);
                  Serial.print("\n");
                  delay(2000);
   
        }
        

Voltage reference for the ADC
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The analog to digital converter uses a voltage reference for comparison with the analog signal it is converting. This reference may be set to `DEFAULT`, `INTERNAL`, or `EXTERNAL`, for 5V, 1.1V or externaly applied voltage to the AREF-pin respectively. The selected reference voltage will be the maximum input voltage for the ADC, i.e. the voltage that will correspond to the digital value of 1023.

.. code-block:: c

        analogReference(EXTERNAL);

The accuracy of the reference voltage directly affects the accuracy of the converted voltage, thus if a external reference is used, it's accuracy should be considered.

..
    Light intensity measurements using a phototransistor
    ----------------------------------------------------

    https://www.arduino.cc/documents/datasheets/HW5P-1.pdf


    Voltage reference experiment using potentiometer
    ------------------------------------------------

    In this example we will be using two potentiometers, one for adjusting the voltage reference, and one for adjusting the input voltage to the ADC.


Digital to analog converter
---------------------------

Alternatively if you need real analog output you may use an external chip, that the Arduino may control by a digital bus such as I2C.



Pulse Width Modulation (PWM)
=============================

What is Modulation?

What is a pulse?

.. note::
   Blackboard explanation.

Analog output
-----------------

This section introduces the pulse width modulation outputs. It is a way of how to trick the having such an output that its value is so-called in between 0-5V. 

The Arduino function associated with analog output signals that we will be using in this tutorial are:

- `analogWrite() <https://www.arduino.cc/reference/en/language/functions/analog-io/analogwrite/>`_

Now we will be pulling up the hood, looking under the hood, digging deeper, pulling back the onion!



Adjusting the light intensity of LED
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**ANNOUNCE:** If you have, use RGB-LED instead. 

In this example we will be adjusting the light intensity of two LED's connected to pin 5, and 6. A push button on pin 10 is used to step through various intensity levels.

.. figure:: ../../fig/digital_input_and_output_bb.png
        :align: center
        :scale: 50


.. note::
   Logic Analyzer demonstration.

.. ref: https://www.instructables.com/id/Arduino-How-to-Control-Servo-Motor-With-Potentiome/


Servo and map function
~~~~~~~~~~~~~~~~~~~~~~~

There are different types of motors which require different driving circuit. Sometimes driving a motor can be challenging but luckily Arduino community presents a plug-and-play option for *servo motor* s. There is an important concept behind driving a servo motor but this theoretical part is going to be examined in the following courses. In this course, we will focus on the programming aspect.

The purpose of this example is to control the position of this little motor you have using a potentiometer.

.. warning:: The servo motor you have is quite fragile. If you try to rotate with hand or hold it while it is trying to rotate, then you may damage the gear box in it.  Please don't.
   
First of all, let's build the following circuit:

.. figure:: ../../../external/fig/servoconnection.jpg
   :align: center


Servo motors have three wires: power, ground, and signal. The power wire is typically red, and should be connected to the 5V pin on the Arduino or Genuino board. The ground wire is typically black or brown and should be connected to a ground pin on the board. The signal pin is typically yellow or orange and should be connected to pin 9 on the board.

The potentiometer should be wired so that its two outer pins are connected to power (+5V) and ground, and its middle pin is connected to analog input 0 on the board.

.. code-block:: c
   :caption: Servo control using potentiometer

   #include <Servo.h>  // add servo library

    Servo myservo;  // create servo object to control a servo

    const int potPin = 0;  // analog pin used to connect the potentiometer
    int val;    // variable to read the value from the analog pin

    void setup() {
        myservo.attach(9);  // attaches the servo on pin 9 to the servo object
        }

        void loop() {
        val = analogRead(potPin);            // reads the value of the potentiometer (value between 0 and 1023)
        val = map(val, 0, 1023, 0, 180);     // scale it to use it with the servo (value between 0 and 180)
        myservo.write(val);                  // sets the servo position according to the scaled value
        delay(15);                           // waits for the servo to get there
    }


Attention that we are writing the potentiometer value directly as the servo angle. What if we want to *check* the value first and switch on an LED *if* it is in a desired range? Then the code will look like.


.. code-block:: c
   :caption: Controlling LED based on Servo angle

    #include <Servo.h>  // add servo library

    Servo myservo;  // create servo object to control a servo

    const int potPin = 0;  // analog pin used to connect the potentiometer
    const int ledPin = 13; // output pin for the LED

    int val;    // variable to read the value from the analog pin

    void setup() {
        myservo.attach(9);  // attaches the servo on pin 9 to the servo object
        }

        void loop() {
        val = analogRead(potPin);            // reads the value of the potentiometer (value between 0 and 1023)
        val = map(val, 0, 1023, 0, 180);     // scale it to use it with the servo (value between 0 and 180)
        myservo.write(val);                  // sets the servo position according to the scaled value
        if(val > 90)
        {
            digitalWrite(ledPin,HIGH);
        }
        else
        {
            digitalWrite(ledPin,LOW);
        }
        delay(15);                           // waits for the servo to get there
    }

.. ref: https://www.allaboutcircuits.com/projects/using-the-arduinos-analog-io/


The function used to output a PWM signal is analogWrite(pin, value). pin is the pin number used for the PWM output. value is a number proportional to the duty cycle of the signal. When value = 0, the signal is always off. When value = 255, the signal is always on. On most Arduino boards, the PWM function is available on pins 3, 5, 6, 9, 10, and 11. The frequency of the PWM signal on most pins is approximately 490 Hz. On the Uno and similar boards, pins 5 and 6 have a frequency of approximately 980 Hz. Pins 3 and 11 on the Leonardo also run at 980 Hz.

To map an analog input value, which ranges from 0 to 1023 to a PWM output signal, which ranges from 0 - 255, you can use the map(value, fromLow, fromHigh, toLow, toHigh) function. This function has five parameters, one is the variable in which the analog value is stored, while the others are 0, 1023, 0 and 255 respectively. 

The frequency of the PWM signal is important to drive a servo motor. Even though the :code:`analogWrite(pin, value)` and :code:`myServo.write(pin,value)` produces PWM signals, their frequencies are different. You can change the :code:`analogWrite(pin, value)` function's frequency by setting the corresponding timers's prescalar. The timer concept is going to be talked in the next section.


Interrupt
===========

A interrupt is some form of external signal that interrupts the main process. When an interrupt occurs the current execution state of the main process is stored, before a different process (the ISR, or interrupt service routine) takes over. When the interrupt service routine has completed, execution control is returned to the main process.

Interrupts are useful for making the system responsive to external events while avoiding constant polling of the possible external event sources. The ISR may simply set a flag, or publish a message in a event queue such that the main process can take appropirate action when it is ready to do so.

The first section of this lecture describes how and when the interrupts are preferable over polling techniques. The following sections explain how the interrupt mechanism works. The second half of the lecture notes, starting with the section "Management of Interrupts," describes the common problems and programming mistakes and their solutions in utilizing interrupts in typical microcontroller applications.


Timing in Microcontrollers
---------------------------

A timer is a specialized type of clock which is used to measure time intervals. A timer that counts from zero upwards for measuring time elapsed is often called a stopwatch. It is a device that counts down from a specified time interval and used to generate a time delay, for example, an hourglass is a timer.

A counter is a device that stores (and sometimes displays) the number of times a particular event or process occurred, with respect to a clock signal. It is used to count the events happening outside the microcontroller. In electronics, counters can be implemented quite easily using register-type circuits such as a flip-flop. [#f2]_

Timers are counters that can be programmed to perform a variety of functions. Following are the typical operation modes and possible applications of timers:

**1. Programmed operation:** A timer can be used as an alarm clock to generate predetermined time delays. The  microprocessor sets the count limit or initializes the counter and enables the count operation. The timer generates an interrupt when the count limit is reached indicating the end of the programmed delay period. This mode of operation utilizes the internal clock and it does not require an external connection.

**2. Gated or triggered operation:** The count operation is controlled by an external signal. There may be several options to start and to stop the counter. In gated mode, the counter is enabled while the external signal is active. A multi-purpose timer allows independent selection of events that start and stop the counter. These events can be a rising or falling edge of the external trigger signal or an internal start/stop command issued by the microprocessor itself. The timer can be programmed to generate an interrupt when the counter stops. The common applications of gated or triggered operation involve any kind of time measurements, such as, measuring revolution time of a motor to detect its rotation speed, or quantification of time-encoded signals.

**3. Clocked operation:** The counter clock is supplied by an external signal while the count operation is enabled by the microprocessor or another timer. The typical applications include quantization of frequency-encoded signals, or position information generated by linear or rotational encoders.

The specifications for a timer are directly related to the requirements of the application:


**1. Maximum clock frequency** determines the timing resolution. The internal clock frequencies available for timer operations depend on the microprocessor clock.

**2. Number of counter bits** determines the count range or the maximum time period that can be measured.
**3. Functionality:** Having a programmable timer does not necessarily mean that it will support all operation modes and gating or triggering options. You need to read the timer description to find out whether it is useful for your application or not. You may as well need additional features such as buffering of timer count results for motor speed measurements. A timer with buffered outputs can store the count result at the end of every motor revolution and re-start the counting process immediately.


**Watchdog timers** are special-purpose timers dedicated to ensure the proper execution of microcontroller functions. The processor is required to restart the watchdog timer before the preset timer period expires and it repeats this operation as long as the watchdog function is enabled. The program written for the processor includes the necessary statements to restart the watchdog timer periodically. If the processor fails to restart the watchdog timer, then this indicates a major functional failure due to corrupt program memory or some other reason. In this case, the watchdog timer resets the processor, forcing initialization of all microcontrollerfunctions [#f1]_.


Interrupts in Arduino
----------------------
There are some important keynotes `About Interrupt Service Routines <https://www.arduino.cc/reference/en/language/functions/external-interrupts/attachinterrupt/>`_ in Arduino. 

Properties of ISR (Interrupt Service Routine):

#. Global variables used in an ISR must be :code:`volatile`.
#. Must be fast and short functions.
#. No delay in the interrupt.
#. An ISR cannot have parameters - no input argument, no output return.
#. Stops **everything** in the main function.
#. :code:`millis()` doesn't work properly, :code:`delayMicroseconds()` works since it does not use any counter.



External Interrupts
-------------------

Created by sensors (via communication channels) or buttons.

An external interrupt is defined as:

.. code-block:: c

   attachInterrupt(digitalPinToInterrupt(pin), ISR, mode)


**Practical exercise:** Modify the RGB-LED code such that the blinking should stop immediately as soon as the button is pressed and should continue again as soon as it is released.


Although we are using the :code:`delay()` function in almost everywhere, it is a very dangerous function. It halts any processing completely. No reading of sensors, mathematical calculations, or pin manipulation can go on during delay function.

    
**Exercise for home:** Modify the interrupt code without using delay functions. There is a very nice project using an LCD in `this link <https://www.youtube.com/watch?v=TD-J7LgrsBQ>`_.



Internal Interrupts
-------------------

Timer based interrupts.

All timers depends on the system clock of your Arduino system. Normally the system clock is 16MHz, but for the Arduino Pro 3,3V it is 8Mhz. So be careful when writing your own timer functions.
The timer hardware can be configured with some special timer registers. In the Arduino firmware all timers were configured to a 1kHz frequency and interrupts are gerally enabled.

Timer0:
Timer0 is a 8bit timer.
In the Arduino world timer0 is been used for the timer functions, like delay() 484, millis() 1.1k and micros() 497. If you change timer0 registers, this may influence the Arduino timer function. So you should know what you are doing.

Timer1:
Timer1 is a 16bit timer.
In the Arduino world the Servo library 811 uses timer1 on Arduino Uno (timer5 on Arduino Mega).

Timer2:
Timer2 is a 8bit timer like timer0.
In the Arduino work the tone() 650 function uses timer2.

Timer3, Timer4, Timer5:
Timer 3,4,5 are only available on Arduino Mega boards. These timers are all 16bit timers. [#f3]_

.. note::
   Since internal timers are created by changing the Timer behaviour through the timer register, we will not cover it here. This subject requires at least intermediate embedded programming skills and datasheet reading. However, this is one of the most important topics in the embedded systems. For those who are willing to learn this topic should know about setup and Use of the Timers* (`AVR130: Setup and Use the AVR Timers <http://ww1.microchip.com/downloads/en/AppNotes/Atmel-2505-Setup-and-Use-of-AVR-Timers_ApplicationNote_AVR130.pdf>`_.


.. note::
   :code:`interrupts()` and :code:`noInterrupts()` for the critical parts of the code. - https://www.arduino.cc/reference/en/language/functions/interrupts/interrupts/


**Practical Exercise:** Let's write a program with an RGB light, blinking each LED for 1 second (reg, green, blue, white) and repeat it infinitely. Also, attach a button that stops blinking of all LEDs. Do not use any interrupt knowledge at first and let's see what happens. Afterwards, define your button as an interrupt and see what changes.




.. rubric:: References
.. [#f1] Izmir Institute of Technology - Department of Electrical and Electronics Engineering *EE443 - Embedded Systems lecture notes - 2013*
.. [#f2] https://www.tutorialspoint.com/embedded_systems/es_timer_counter.htm
.. [#f3] https://www.robotshop.com/community/forum/t/arduino-101-timers-and-interrupts/13072

