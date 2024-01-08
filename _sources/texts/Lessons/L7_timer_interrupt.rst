.. _L7_timer_interrupt:

******************************************************************
Interrupts and timers
******************************************************************

.. role:: ccode(code)
        :language: c

This lesson covers the two important and often related concepts of interrupts and timers in microcontrollers.

Interrupts
==========

A interrupt is some form of external signal that interrupts the main process. When an interrupt occurs the current execution state of the main process is stored, before a different process (the ISR, or interrupt service routine) takes over. When the interrupt service routine has completed, execution control is returned to the main process.

Interrupts are useful for making the system responsive to external events while avoiding constant polling of the possible external event sources. Sometimes the ISR may simply set a flag, or publish a message in a event queue such that the main process can take appropriate action when it is ready to do so.

..
    .. figure:: ../../../external/fig/check-phone.gif
    :alt: ASCII table
    :align: center

.. image:: ../../../external/fig/check-phone.gif
   :width: 49%
.. image:: ../../../external/fig/phone-rings.gif
   :width: 45%


--------------------------------POLLING-----------------------------------VS----------------------------------INTERRUPT----------------------------

.. The first section of this lecture describes how and when the interrupts are preferable over polling techniques. The following sections explain how the interrupt mechanism works.

.. The second half of the lecture notes, starting with the section "Management of Interrupts," describes the common problems and programming mistakes and their solutions in utilizing interrupts in typical microcontroller applications.


An interrupt is a signal that tells the processor to immediately stop what it is doing and handle some high priority processing.  That high priority processing is called an Interrupt Handler. An interrupt handler is like any other void function.  If you write one and attach it to an interrupt, it will get called whenever that interrupt signal is triggered.  When you return from the interrupt handler, the processor goes back to continue what it was doing before. [#f4]_

Interrupts can be generated from several sources:

#. Timer interrupts from one of the Arduino timers.
#. External Interrupts from a change in state of one of the external interrupt pins.
#. Pin-change interrupts from a change in state of any one of a group of pins.


Using interrupts, you don’t need to write loop code to continuously check for the high priority interrupt condition.  You don't have to worry about sluggish response or missed button presses due to long-running subroutines.  

The processor will automagically stop whatever it is doing when the interrupt occurs and call your interrupt handler.  You just need to write code to respond to the interrupt whenever it happens. [#f4]_

Types of Interrupts  
There are two types of interrupts:

**Hardware Interrupt:** It happens when an external event occurs like an external interrupt pin changes its state from LOW to HIGH or HIGH to LOW.

**Software Interrupt:** It happens according to the instruction from the software. For example Timer interrupts are software interrupt.

.. admonition:: **Start with an exercise**
   :class: myownstyle

   Use 3 LEDs attached to pin numbers 9,10,11. Turn them on one by one. When all are turned on, turn them of one by one in reverse order as shown in the GIF below. Wait 1 second every time you reverse the order.

  .. figure:: ../../../external/fig/led-on-off-in-order.gif

  Now attach a button to the pin number 2 and update your code such that a counter will count everytime you press the button. Observe any problems?

..
        Solution is in ../../../projects/led-on-off-in-order.gif


Interrupts in Arduino
-----------------------

There are some important keynotes `About Interrupt Service Routines <https://www.arduino.cc/reference/en/language/functions/external-interrupts/attachinterrupt/>`_. 

Properties of ISR (Interrupt Service Routine):

#. Global variables used in an ISR must be declared :code:`volatile`.
#. ISR should normally be fast and short functions.
#. No delay in the ISR.
#. Interrupts are disabled while the ISR is executed. They can be enabled, but this should normally be avoided as it might have unpredictable consequences.
#. An ISR cannot have parameters - no input argument, no output return.
#. Stops **everything** in the main function.
#. :code:`millis()` doesn't work properly, :code:`delayMicroseconds()` works since it does not rely on interrupts.

Interrupts can be:

- Internal: Timers, ADC conversion complete, hardware faults, etc.
- External: digital pins, or communication channels (UART/SPI/I2C)


Interrupts can be enabled or disabled globally, i.e all interrupt sources can be enabled or disabled. This can be useful if it is important that some part of your code is able to run without interruption. There could be a multitude of reasons why your code should run without interruption. One possible reason is if the code is very timing critical, once it is started it has to execute as fast as possible. Another possibility could be that your code is manipulating some part of memory which some ISR is also manipulating. If your code is only half way through the manipulation when the ISR starts doing some other operation to the same memory (e.g. the same variable), the result will be unpredictable. This is known as a race condition.

- Enable interrupts by calling the function: :ccode:`interrupts()`, or: :ccode:`sei()`
- Disable interrupts by calling the function: :ccode:`noInterrupts()`, or: :ccode:`cli()`

Note that interrupts are enabled by default when your program starts.

External Interrupts
--------------------

There are multiple options for how an external interrupt is triggered. This can be configured by the Arduino library, but since it is really a hardware feature of the microcontroller the supported features may vary from one Arduino type to the next. Generally there are five conditions for triggering interrupts in the Arduino library, but only the first four are supported by the Atmega328p of the Arduino UNO:

.. or if the signal is in low state at 0 or if the signal is in high state trigger 5v.

#. Change: When signal change (sensitive to both rising and falling edge).
#. Rising: On a rising edge (the signal going from low to high, or from 0v to 5v).
#. Falling: On a falling edge (the signal going from high to low, or from 5v to 0v).
#. Low: Low is a continuous trigger whenever the signal is low, or 0v. I.e. if the voltage is low, the ISR will fire again as soon as it completes.
#. High: High is a continuous trigger whenever the signal is high, or 5v. (Not supported in Arduino UNO)

.. The syntax which are going to be attach interrupt and specify the pin, e.g. pin number 2 than ISR is the function which is going to be call and mode tells that whenever the interrupts is been triggered.

.. Created by sensors (via communication channels) or buttons.

An external interrupt is configured by the function call:

.. code-block:: c

   attachInterrupt(digitalPinToInterrupt(some_pin_number), ISR_function_callback, mode)

The first parameter to the :ccode:`attachInterrupt()` function is the interrupt number. In order to have a more flexible and readable code, you should use the function :ccode:`digitalPinToInterrupt()` to translate the pin number to the interrupt number. The second parameter, which is named :ccode:`ISR_function_callback` in the above example, is the name of a function that you have defined. Any valid function name can be provided, and it should not include the parentheses that you normally use when calling a function. This function will be called whenever the interrupt is triggered. Finally the :ccode:`mode` parameter is used to select which events on the external pin will cause the interrupt to trigger. As described previously, the following modes are available: :ccode:`CHANGE`, :ccode:`RISING`, :ccode:`FALLING`, :ccode:`LOW`, :ccode:`HIGH`.

In order to disable the interrupt the following function call is used:

.. code-block:: c

    detachInterrupt(digitalPinToInterrupt(some_pin_number))

.. admonition:: **Continue the exercise**
   :class: myownstyle

   Now solve the count problem with interrupts.


Exercise: change the trigger criteria
--------------------------------------

In this exercise you will change the criteria for how the external interrupt is triggered, and experience the effect it has on the behaviour of you program. You should use pull-up resistors, and allow the bush button to pull down the logic level of the input pin. This will allow you to more easily test the continuous trigger on logic low.

.. literalinclude:: ../../../projects/platformio/interrupt/external-interrupt-counter/src/main.cpp
    :language: c
    :linenos:


#. Upload and test the provided code.
#. Try all the different trigger modes which are supported. Take note on the behaviour of the counter in each case.
#. Extend the program with the ability to toggle the state of a LED on the rising edge interrupt.

..
    Exercise: Led blink start/stop
    ------------------------------

#. Write a program to blink a normal, or RBG-LED using :ccode:`millis()` for the delay.
#. Modify the code such that the blinking should stop immediately as soon as a button is pressed, and should continue again as soon as it is released. Use an external interrupt to detect the rising and falling edge of the push button.
    
.. Modify the RGB-LED code such that the blinking should stop immediately as soon as the button is pressed and should continue again as soon as it is released.


Demo: external interrupt on rising edge of both pins
----------------------------------------------------

The following example illustrates how to enable the ISR on both of the external interrupt pins. The ISR toggles an external LED, as well as increasing the value of a variable in order to keep track of how many times the ISR has been executed. Due to mechanical switch bouncing, the ISR can sometimes fire multiple times on each push of the button.

.. literalinclude:: ../../../projects/platformio/interrupt/external-interrupts-demo/src/main.cpp
    :language: c
    :linenos:

Instead of continuously printing the counter value with a interval of 1 second, it is also possible to only print it after a ISR has been executed:

.. literalinclude:: ../../../projects/platformio/interrupt/external-interrupt-demo2/src/main.cpp
    :language: c
    :linenos:



Exercise: Using external interrupts to count switch bouncing
-------------------------------------------------------------

In order to determine how much your switch is actually bouncing, it is possible to use an external interrupt pin. The previous example illustrates this to some extent, but in this exercise you will develop a program which can be used to test the quality of your push buttons.

#. Connect a push button to one of the external interrupt pins (pin 2, or 3 on the Arduino UNO).
#. Configure the ISR to trigger on both rising and falling edge of the signal on the pin. Test by toggling a LED inside the ISR.
#. Increment two variables inside the ISR, one for rising, and one for falling edge. Add code inside your :code:`loop()` to print the value of the variables to the UART whenever they have changed. Do not use :code:`Serial.print` inside the ISR.
#. Add some code to keep track of the maximum number of times the switch has bounced. I.e. compare the current number of bounces to the previous maximum, and update if required. For this to work you must somehow determine when to stop counting. I.e. if you press the button twice (even if you do it quickly) it should count as two pushes, not as bouncing.
#. Add some code to keep track of the timing between each time the ISR is executed. Print the time interval ot the UART.

Pin change Interrupts
----------------------

.. TODO: Note for Gizem: I am not sure if we should include this or not. If you do not want to include it, you can move it to the additional chapter :)

The external interrupts are only available on pin 2, and 3 on the Arduino UNO (this is a limitation of the microcontroller). The Atmega328p also support a feature known as pin change interrupt on all digital inputs. The pin change interrupt will cause an interrupt if any of the enabled pins in a group is changed. I.e. the same ISR will run regardless of which pin in the group has changed. This is not as flexible as the *external interrupt*, but it can still be useful. In many cases the first job of the pin change ISR will be to check (E.g. :ccode:`digitalRead`) which pin generated the interrupt request.

The following example illustrates how to use the pin change interrupts on pin 8, 9 and 10. Since the feature is not supported by the Arduino library we have to access some hardware registers directly. Refer to the datasheet of the Atmega328p for understanding the purposes of each of the bits in the configuration registers.

A special function is defined for configuration of the pin change interrupt system. Without going in to the details, this function can be copied, and used to configure pin 8, 9, and 10 for pin change interrupt.

.. code-block:: c

    void configure_pin_change_interrupt(){

      PCICR |= (0 << PCIE2) | (0 << PCIE1) | (1 << PCIE0); // Enable pin change interrupt on PCINT0 to PCINT7

      /**
       * Pin 8 -> PB0 -> PCINT0
       * Pin 9 -> PB1 -> PCINT1
       * Pin 10 -> PB2 -> PCINT2
       */
      //PCMSK2;
      //PCMSK1;
      PCMSK0 |= (1 << PCINT2) | (1 << PCINT1) | (1 << PCINT0);

    }

Further down in the code the function :ccode:`ISR (PCINT0_vect) {` is the definition of the ISR which execute when the pin change interrupt is triggered.

.. literalinclude:: ../../../projects/platformio/interrupt/pin-change-interrupt-demo/src/main.cpp
    :language: c
    :linenos:


Internal Interrupts
------------------------------

Internal interrupt are interrupt which are initiated by some internal hardware of the microcontroller. One example is the timer based interrupts, which are issued on some event from one of the hardware timers. E.g. when a certain time limit has expired. This particular interrupt type will be covered in the section about timers.


Timers
======

A timer in this context is a specialized type of hardware peripheral which is used to measure time intervals. On the fundamental level the hardware timers are built around counters, which count on the edges of some external clock signal. A timer that counts from zero upwards and stops on some external event, for measuring time elapsed until the event is often called a stopwatch. On the other hand, a device that counts down from a specified initial time, and generates an event when it reaches zero is a timer.

A counter is a device that stores (and sometimes displays) the number of times a particular event or process occurred, with respect to a clock signal. It is used to count the events happening outside the microcontroller. In electronics, counters can be implemented quite easily using register-type circuits such as a flip-flop. [#f2]_

Timers are counters that can be programmed to perform a variety of functions. Following are the typical operation modes and possible applications of timers:

**1. Programmed operation:** A timer can be used as an alarm clock to generate predetermined time delays. The  microprocessor sets the count limit or initializes the counter and enables the count operation. The timer generates an interrupt when the count limit is reached indicating the end of the programmed delay period. This mode of operation utilizes the internal clock and it does not require an external connection.

**2. Gated or triggered operation:** The count operation is controlled by an external signal. There may be several options to start and to stop the counter. In gated mode, the counter is enabled while the external signal is active. A multi-purpose timer allows independent selection of events that start and stop the counter. These events can be a rising or falling edge of the external trigger signal or an internal start/stop command issued by the microprocessor itself. The timer can be programmed to generate an interrupt when the counter stops. The common applications of gated or triggered operation involve any kind of time measurements, such as, measuring revolution time of a motor to detect its rotation speed, or quantification of time-encoded signals.

**3. Clocked operation:** The counter clock is supplied by an external signal while the count operation is enabled by the microprocessor or another timer. The typical applications include quantization of frequency-encoded signals, or position information generated by linear or rotational encoders.

The specifications for a timer are directly related to the requirements of the application:


**1. Maximum clock frequency** determines the timing resolution. The internal clock frequencies available for timer operations depend on the microprocessor clock.

**2. Number of counter bits** determines the count range or the maximum time period that can be measured.

**3. Functionality:** Having a programmable timer does not necessarily mean that it will support all operation modes and gating or triggering options. You need to read the timer description to find out whether it is useful for your application or not. You may as well need additional features such as buffering of timer count results for motor speed measurements. A timer with buffered outputs can store the count result at the end of every motor revolution and re-start the counting process immediately.


**Watchdog timers** are special-purpose timers dedicated to ensure the proper execution of microcontroller functions. The processor is required to restart the watchdog timer before the preset timer period expires and it repeats this operation as long as the watchdog function is enabled. The program written for the processor includes the necessary statements to restart the watchdog timer periodically. If the processor fails to restart the watchdog timer, then this indicates a major functional failure due to corrupt program memory or some other reason. In this case, the watchdog timer resets the processor, forcing initialization of all microcontroller functions [#f1]_.

Timers in Arduino
------------------

Normally the timers are clocked by the system clock of your Atmega328p. The system clock is 16MHz, but a prescaler is available to divide this clock by some configurable constant in order to have a slower timing operation. The timer hardware is configured by means of some special timer registers. In the Arduino firmware all timers were configured to a 1kHz frequency and interrupts are generally enabled.


**Timer0:**
Timer0 is a 8bit timer.
In the Arduino world timer0 is been used for the timer functions, like [delay() 811](http://arduino.cc/en/Reference/Delay),[ millis() 1.8k](http://arduino.cc/en/Reference/Millis) and[ micros() 804](http://arduino.cc/en/Reference/Micros). If you change timer0 registers, this may influence the Arduino timer function. So you should know what you are doing.

**Timer1:**
Timer1 is a 16bit timer.
In the Arduino world the [Servo library 1.3k](http://arduino.cc/en/Reference/Servo) uses timer1 on Arduino Uno (timer5 on Arduino Mega). 

**Timer2:**
Timer2 is a 8bit timer like timer0.
In the Arduino work the [tone() 1.1k](http://arduino.cc/en/Reference/Tone) function uses timer2.

Timer3, Timer4, Timer5:
Timer 3,4,5 are only available on Arduino Mega boards. These timers are all 16bit timers. [#f3]_

.. seealso:: `Arduino timers for *super nerd*s <https://www.robotshop.com/community/forum/t/arduino-101-timers-and-interrupts/13072>`_, if you would like to know all the low level details of the timers.

Different Timer purposes
------------------------------

Timer Overflow:
Timer overflow means the timer has reached is limit value. When a timer overflow interrupt occurs, the timer overflow bit TOVx will be set in the interrupt flag register TIFRx. When the timer overflow interrupt enable bit TOIEx in the interrupt mask register TIMSKx is set, the timer overflow interrupt service routine ISR(TIMERx_OVF_vect)  will be called.

Output Compare Match:
When a output compare match interrupt occurs, the OCFxy flag will be set in the interrupt flag register TIFRx . When the output compare interrupt enable bit OCIExy in the interrupt mask register TIMSKx is set, the output compare match interrupt service ISR(TIMERx_COMPy_vect) routine will be called.

Timer Input Capture:
When a timer input capture interrupt occurs, the input capture flag bit ICFx will be set in the interrupt flag register TIFRx. When the input capture interrupt enable bit  ICIEx in the interrupt mask register TIMSKx is set, the timer input capture interrupt service routine ISR(TIMERx_CAPT_vect) will be called.

PWM and timer
There is fixed relation between the timers and the PWM capable outputs. When you look in the data sheet or the pinout of the processor these PWM capable pins have names like OCRxA, OCRxB or OCRxC (where x means the timer number 0..5). The PWM functionality is often shared with other pin functionality.
The Arduino has 3Timers and 6 PWM output pins. The relation between timers and PWM outputs is:
Pins 5 and 6: controlled by timer0
Pins 9 and 10: controlled by timer1
Pins 11 and 3: controlled by timer2

Servo Library uses Timer1. You can’t use PWM on Pin 9, 10 when you use the Servo Library on an Arduino

Timer interrupts
-----------------

On the Atmega328p the timer clock signal is normally derived from the system clock, but typically scaled down to a lower rate. The system clock is 16 MHz on the Arduino UNO, but for the Arduino Pro 3,3V it is 8Mhz. The timer hardware can be configured with some special timer registers. In the Arduino firmware all timers are configured to a 1kHz frequency and interrupts are generally enabled.

.. So be careful when writing your own timer functions.


.. note::
   Modification of timer behaviour through register manipulation is rather advanced, and will not be covered here. This subject requires at least intermediate embedded programming skills and datasheet reading. However, this is an important topic to know for the embedded systems developer. For those who are willing to learn this topic the following application note could be useful: `AVR130: Setup and Use the AVR Timers <http://ww1.microchip.com/downloads/en/AppNotes/Atmel-2505-Setup-and-Use-of-AVR-Timers_ApplicationNote_AVR130.pdf>`_.


..   :code:`interrupts()` and :code:`noInterrupts()` for the critical parts of the code. - https://www.arduino.cc/reference/en/language/functions/interrupts/interrupts/
.. note::
   :code:`interrupts()` and :code:`noInterrupts()` for the critical parts of the code. - https://www.arduino.cc/reference/en/language/functions/interrupts/interrupts/


..
   **Practical Exercise:** Let's write a program with an RGB light, blinking each LED for 1 second (reg, green, blue, white) and repeat it infinitely. Also, attach a button that stops blinking of all LEDs. Do not use any interrupt knowledge at first and let's see what happens. Afterwards, define your button as an interrupt and see what changes.


Using a library to control timer 1
----------------------------------

Since direct register manipulation is cumbersome, and also typically specific to only one, or a limited number of specific hardware platforms, it is often preferable to use a library to configure the timers. In this section we will cover the *TimerOne* library, which allows us to configure timer 1.

The analog write function for pin 9, and 10 on the Arduino depends on timer 1. If you are using these libraries you will not be able to use those pins for PWM (at least it will not be reliable).

The following code listing shows how to blink a led at a rate of 1 Hz, using the TimerOne library:

.. literalinclude:: ../../../projects/hardware-timers/timer-one-lib-demo/src/main.cpp
    :language: c
    :linenos:

The :ccode:`Timer1.initialize(500000);` method sets the timeout to :math:`500000` microsecond, or :math:`0.5` seconds. The maximum value for the time period is :math:`8388480` microseconds, or about :math:`8.3` seconds.


The timer can be started (:ccode:`Timer1.start()`), stopped (:ccode:`Timer1.stop()`), and restarted (:ccode:`Timer1.restart()`) on demand by the application.

.. todo: add more details from: https://playground.arduino.cc/Code/Timer1/

Exercise: User timer ISR for polling digital inputs
---------------------------------------------------

This exercise is a little bit challenging, but demonstrate a very relevant problem and common use case for a timer.

#. Use the TimerOne library to generate an interrupt with a interval of 5 millisecond.
#. Use the timer ISR to poll the state of three push buttons.
#. Store the state of the push buttons, and compare them to the previous state. If the state remains stable for two invocations of the ISR, they should be considered valid (i.e. debounced).
#. Write a function :ccode:`uint8_t get_btn_rising_edge_event(uint8_t btn)` which returns true the first time it is called for a given button, after the button has had a rising edge. The function must reset a rising edge detected flag back to zero after invocation, in order to ensure that successive calls does not return true unless the flag has again been set by the ISR.
#. Write a function :ccode:`uint8_t get_btn_falling_edge_event(uint8_t btn)` which returns true the first time it is called for a given button, after the button has had a falling edge.
#. Add a routine to :ccode:`loop()` which prints a message on rising, and falling edge of each push button.

Exercise: RGB led blinking
---------------------------

#. Write a program which blinks a RGB lamp, blinking each LED for 1 second (reg, green, blue, white) and repeat it infinitely. The blink frequency should be 5 Hz. Do not use any interrupt or hardware timer knowledge at first and let's see what can be done to solve the problem. (you may use :ccode:`millis()`)
#. Attach a button that stops blinking of all LEDs. 
#. Modify (or rewrite) your program to use the TimerOne library for the timing, and define your button as an external interrupt.
#. Write a short summary of the advantages and disadvantages for each of your two solutions.


Recursive functions
====================
Since we know conditionals, functions, loops and arrays we can talk about common paradigm in programming languages: **Recursive Functions**. There are still discussions about whether recursive functions are bad or good, they are staple in procedural programming languages (like C, Fortran, Pascal) and also commonly used in object-oriented programming languages (like C++, C#, Python). 

By definition a recursive function is one that calls back to itself. The most common example might be the Fibonacci.

.. code-block::

  int ledPin = 13;
  int i = 0;

  void setup() {
    Serial.begin(9600);
    pinMode(ledPin, OUTPUT);
  }

  void loop() {

    digitalWrite(ledPin, HIGH);
    delay(100);
    digitalWrite(ledPin, LOW);
    delay(fibonacci(i)*100);
    i++;
  }

  int fibonacci(int n) {
    if (n <=1) {
      return n;
    }
    else {
      return fibonacci(n-1)+fibonacci(n-2);
    }
  }


**The good** about recursive functions are generally shorter and more elegant. **The bad** sides of the recursion is mainly that recursive functions are less efficient than their iterative counterparts. Additionally, they are subject to the perils of stack overflows, which is *probably the most common* error in early programming languages that happens when program tries to use more memory space than the call stack has available. 

.. seealso::

  `https://stackoverflow.com <https://stackoverflow.com/>`_ is the number one website for programmers!


From Arduino perspective, the situation is 50/50. Recursive functions are resource intensive and we don't like using any resources (neither memory nor power) on microcontrollers. An embedded software project has to do a resource budget. However, recursive functions are easy to implement and comprehend. Particularly sensor applications where there is a data to be manipulated based on previous readings, recursive functions can be an option. 

One last thing to note, it does not take too long to fill the 2KBytes memory of Arduino. If you need to recurse too much than you may need to free some memory. We will not get into details of it since it has too many branches when it comes to embedded systems programming. Particularly Run-time performance constraints and Memory constraints are acknowledged as the most effort requiring skills in all software applications. Since these are the most critical points in microcontrollers, embedded system software development is scientifically approved the `most difficult of programming skills to master <https://en.wikipedia.org/wiki/COCOMO>`_.


Random
=======
What is random? Is there such a thing?

The arduino :code:`random()` function created `Pseudorandom number <https://en.wikipedia.org/wiki/Pseudorandom_number_generator>`_. However, it is not truely random. It creates random numbers based on an initial value. This initial value is the :code:`time(0)` where Arduino is first powered.


.. code-block:: c

  long randNumber;

  void setup() {
    Serial.begin(9600);
  }

  void loop() {
    // print a random number from 0 to 299
    randNumber = random(300);
    Serial.println(randNumber);

    // print a random number from 10 to 19
    randNumber = random(10, 20);
    Serial.println(randNumber);

    delay(50);
  }

It is quite a special subject in all programming languages. By providing different *seed* s. It is a bit of an issue in C# to create truely random but we are slightly luckier on the Arduino side.

.. code-block:: c

  void setup() {
    Serial.begin(9600);

    // if analog input pin 0 is unconnected, random analog
    // noise will cause the call to randomSeed() to generate
    // different seed numbers each time the sketch runs.
    // randomSeed() will then shuffle the random function.
    randomSeed(analogRead(0));
  }

The :code:`randomSeed(analogRead(0));` creates a random seed based on the noise signal on A0 pin.

Check this out:

.. code-block:: c

  long randNumber;

  void setup() {
    Serial.begin(9600);
    // randomSeed(5);
    randomSeed(analogRead(0));
  }

  void loop() {
    // print a random number from 0 to 299
    randNumber = random(300);
    Serial.print(randNumber);
    Serial.print("\t");
    
    // print a random unsigned int
    randNumber = rand();
    Serial.print(randNumber);
    Serial.print("\t");
    
    // print a random number from 10 to 19
    randNumber = random(10, 20);
    Serial.println(randNumber);

    delay(500);
  }

Summary
=============

**Properties TL;DR:**

- Interrupt Service Routine function (ISR) must be as short as possible.

- Delay () function doesn’t work inside ISR and should be avoided.

- No input no output

- No serial. (if you have to, then maybe Serial.print())

- delayMicroseconds() works 1-2

.. code-block:: c

    void myDelay(int x)   {
        for(int i=0; i<=x; i++)   
        {
            delayMicroseconds(1000);
        }
    }

- variables must be volatile

- Sometimes need to disable interrupts:

.. code-block:: c

    noInterrupts ();
    long myCounter = isrCounter;  // get value set by ISR
    interrupts ();



The most basic code:

.. code-block:: c

    const byte ledPin = 13;
    const byte interruptPin = 2;
    volatile byte state = LOW;

    void setup() {
        pinMode(ledPin, OUTPUT);
        pinMode(interruptPin, INPUT_PULLUP);
        attachInterrupt(digitalPinToInterrupt(interruptPin), blink, CHANGE);
    }

    void loop() {
        digitalWrite(ledPin, state);
    }

    void blink() {
        state = !state;
    }


Or modify:

.. code-block:: c

    const byte ledPin = 13;
    const byte interruptPin = 2;
    volatile byte state = LOW;

    void setup() {
        pinMode(ledPin, OUTPUT);
        pinMode(interruptPin, INPUT_PULLUP);
        attachInterrupt(digitalPinToInterrupt(interruptPin), blink, CHANGE);
    }

    void loop() {
        delay(1000);
    }

    void blink() {
        state = !state;
        digitalWrite(ledPin, state);
    }





.. rubric:: References
.. [#f4] https://learn.adafruit.com/multi-tasking-the-arduino-part-2/what-is-an-interrupt
.. [#f1] Izmir Institute of Technology - Department of Electrical and Electronics Engineering *EE443 - Embedded Systems lecture notes - 2013*
.. [#f2] https://www.tutorialspoint.com/embedded_systems/es_timer_counter.htm
.. [#f3] https://www.robotshop.com/community/forum/t/arduino-101-timers-and-interrupts/13072

