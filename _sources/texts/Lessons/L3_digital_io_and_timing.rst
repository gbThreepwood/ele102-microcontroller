.. _digital_io_and_timing:

*******************************************************
Digital I/O, and timing
*******************************************************

.. role:: ccode(code)
        :language: c


.. In this lesson we will practice what we learned about last lecture on digital I/O, serial monitor interaction.


Using digital inputs
====================

Digital inputs have already been covered in detail in a previous lesson. In this lesson the focus is on how to best utilize the digital input signal in software.


Edge detection
===============
When reading a digital input it is often desirable to determine the instant it is changing, and how it is changing. I.e. whether it is a rising, or a falling edge. There are several approaches we can take to solve this problem, but in this section we focus on solutions involving the sampling of the digital input. I.e. we are continuously checking the state of the digital input with a certain time interval. This approach is fine for slowly changing signals such as push buttons. Actually it is not only fine, it is often the recommended way to deal with slow signals.

In order to detect the rising edge of a digital input by the sampling method, we continuously compare the current state of the input, to the state at the previous iteration (the previous time we checked it). If the previous value was *low*, and the current value is *high*, we have a rising edge. The same logic can be applied it the reverse direction to detect the falling edge.

Let’s think about what happens when you press a button. In this example (and the last) we have a digital pin connected to 5 volts through a tilt sensor. When the system is vertical, two pins are in contact. The 5 volts is applied to the digital pin. However, when you tilt the system, the connection is broken. Therefore, the system is not in *OFF state* but in an *unknown state*. Consider the figure below:

.. figure:: ../../../external/fig/switchFailure.png
   :align: center

.. ref: https://www.programmingelectronics.com/tutorial-18-state-change-detection-and-the-modulo-operator-old-version/

.. figure:: ../../../external/fig/edgedetection.jpg
   :align: center

   Source: `programmingelectronics.com <https://www.programmingelectronics.com/tutorial-18-state-change-detection-and-the-modulo-operator-old-version>`_


Understanding Pull-up / Pull-down resistors
-------------------------------------------

As already discussed in terms of push buttons, when using digital inputs it is often required to add resistors to either pull up, or pull down the potential at the input. This is required because the input impedance of the digital input is very high, and the state may change randomly if it is not forced to a known state. For our convenience the Atmega 328 used in the Arduino UNO has internal pull up resistors that may be enabled or disabled in software. Alternatively you may add external resistors.

The size of the resistors is not critical, but it should not be selected on random either. A to small resistor may cause excessive current, while a to large resistor will defeat the purpose of trying to pull towards a given potential. I.e. the resistor value should be far away from the value of the input impedance. In practice a 10k resistor is often used. 

.. The analog pins also have pull-up resistors, which work identically to pull-up resistors on the digital pins. They are enabled by issuing a command such as :code:`pinMode(A0, INPUT_PULLUP);  // set pull-up on analog pin 0`

.. figure:: ../../../external/fig/Pull-up-and-Pull-down-Resistor.png
   :alt: Arduino with pull-up, and pull-down resistors on push buttons
   :align: center
   
   Source: `circuitdigest.com <https://circuitdigest.com/tutorial/pull-up-and-pull-down-resistor>`_

.. Be aware however that turning on a pull-up will affect the values reported by analogRead(). The pull up should be disabled before you consider using a given pin as a analog input.

The following figure depicts the connection of two push buttons to the Arduino. For the leftmost button the resistor in the figure pulls the input low, and the push button is connected in such a way that it can pull it up. For the button to the right the configuration is opposite.

.. figure:: ../../fig/fritzing/arduino_two_pb_pull_up_pull_down_bb.png
  :alt: Arduino with external LED, and two push buttons
  :align: center
  :scale: 50


.. .. figure:: ../../fig/digital_input_demo_bb.png
        :alt: Arduino with external LED, and external push button
        :align: center
        :scale: 50

It is very important to realize that the default state of a digital input depends on whether the input is pulled up, or pulled down. If the input is pulled down by a resistor, and the push button pulls it up, then a push on the button will make the input logical **HIGH**. If on the other hand the input is pulled up by a resistor, and a push on the button pulls it down, pushing the button will make the input logical **LOW**

It is not important which of the two you choose, because it is easy to invert the state in software. But it is important to realize the difference, in order to know when you have to invert it in software.



Edge detection algorithm
------------------------

.. todo:: Describe a typicall edge detection algorithm

.. Hands-on Exercises
.. ====================
.. Time to use expand the knowledge by doing!

Simple button press counter
----------------------------

Imagine that you develop a system that counts how many times a button pressed. It can be on a secure door, a keyboard software or a simple knitting row counter! Here is a simple system: 

.. figure:: ../../../external/fig/simple_button_press.png
   :align: center


Here is the code template:

.. code-block:: c

    // define led_pin at pin nr.2;
    // define button_pin at pin nr.11;
    // define a counter variable
    void setup()
    {
        // set input/output pin modes
        // start a serial communication between Arduino and the PC
    }

    void loop()
    {
        // read button value and keep it in a variable
        // increase the counter variable
        // change the LED state accordingly
        // Print the button state and the total count on the same line with a *tab* space
        delay(10);
    }

The following code listing is one possible solution:

.. code-block:: c

    const uint8_t led_pin = 2;
    const uint8_t button_pin = 11;
    uint16_t count = 0;
    void setup()
    {
        pinMode(led_pin, OUTPUT);
        pinMode(button_pin, INPUT);
        Serial.begin(9600);
    }

    void loop()
    {
        bool button_state = digitalRead(button_pin);
        count += button_state;
        digitalWrite(led_pin, button_state);
        Serial.print(button_state);
        Serial.print("\t");
        Serial.println(count);
        delay(10);
    }


.. .. note:: Note that how different serial print methods are used.

Can you run and determine if the system meets your requirements, or if something is off? What if you change the :code:`delay()` duration? Would it be *perfect* then?

..
    What if you move :code:`uint16_t count = 0;` in a function, let say in :code:`setup()` or :code:`loop()`. Play with this code and define the problem please.


Simple button LED state change
-------------------------------

Maybe we can modify the code such that the LED is not *directly* controlled by the button value but it can be controlled by its *change*. Design a system that changes the LED state in every button press.

.. code-block:: c

    // define led_pin at pin nr.2;
    // define button_pin at pin nr.11;
    // define 2 variables to keep button past and current states
    // (make sure that you set an initial value for the past state)
    // define a variable to keep LED state

    void setup(){
        // set input/output pin modes
    }

    void loop (){
        // read button value and keep it in a variable (current state)
        // IF there is a difference between past and current state of the button{
            // toggle LED state
        }
        // change the LED state accordingly
        delay(100);
    }


Again the following code listing is one possible solution:

.. code-block:: c

    const uint8_t led_pin = 2;
    const uint8_t button_pin = 11;
    bool button_state_now = 1;
    bool button_state_prev = 0;
    bool led_state = 0;

    void setup()
    {
        // set input/output pin modes
    pinMode(led_pin, OUTPUT);
    pinMode(button_pin, INPUT);
        // start a serial communication between Arduino and the PC
    Serial.begin(9600);
    }

    void loop()
    {  
    button_state_now = digitalRead(button_pin);

    if((button_state_now != button_state_prev) && (button_state_now == LOW)){
        led_state = !led_state;
    }
    
    digitalWrite(led_pin, led_state);
    button_state_prev = button_state_now;
    
    delay(100);
    }

Exercise: Rising and falling edge detection
-------------------------------------------

For this exercise you can use the following connections:

.. figure:: ../../fig/fritzing/arduino_two_pb_one_led_bb.png
  :alt: Arduino with external LED, and two push buttons
  :align: center
  :scale: 50


#. Write a program which detect the rising edge of a push button. The program should send the text "rising edge detected" to the serial port on each rising edge.
#. Extend the program to also detect the falling edge, and send the text "falling edge detected".
#. Extend the program to toggle a LED on the rising edge.
#. Take note on how many rising and falling edge detected messages you receive each time you push the button. If you receive more than one message on each button push this is likely due to mechanical bouncing which is covered in the next section.
#. Extend the program to detect the rising and falling edge of a second push button.


Exercise: Rising edge counter
-----------------------------

#. Write a program which counts the rising edges of the push button. On each rising edge, the value of the counter should be printed to the serial port.
#. Push the button 10 times, and take note of the value of your counter. If the value is higher than 10, it means that the button is bouncing (or that you made a mistake in your program)





Efficient timing in the microcontroller
========================================

We have previously used the :code:`delay()` function to perform blocking delays in our code. This way of performing delays quickly becomes problematic as our software is growing in size. A function call such as :code:`delay(1000)` will make the controller wait and do nothing for the duration of one second. This makes implementing even simple programs such as one that blinks two LEDs at different rates a challenge.

One alternative approach involves the use of the function :code:`millis()`. The function returns the number of milliseconds since the last time the microcontroller was reset. This can be exploited by constantly checking the current value returned from :code:`millis()`, and executing some code only when the returned value has increased by the same amount as our desired delay. When our code executes after the delay, we must also store the current value from :code:`millis()`, so that we again can compare it to future values from :code:`millis()`.

.. code-block:: C

    uint16_t delay_time = 1200; // 1200 ms delay
    uint32_t old_millis = 0;

    void loop(){
        if((uint32_t)(millis() - old_millis >= delay_time)){
            // Blink a LED, or something...
            old_millis = millis();
        }
    }

Alternatively you can modify the code to only use a single call to :code:`millis()`. This is slightly better, as there is a risk that the :code:`millis()` call in :code:`old_millis = millis()` returns a slightly larger value than the :code:`millis()` in :code:`if((uint32_t)(millis() - old_millis >= delay_time))`. Hence the timing could be more accurate in the following code: 

.. code-block:: C

    uint16_t delay_time = 1200; // 1200 ms delay
    uint32_t old_millis = 0;

    void loop(){

        uint32_t current_millis = millis();

        if((uint32_t)(current_millis - old_millis >= delay_time)){
            // Blink a LED, or something...
            old_millis = current_millis;
        }
    }

Additionally it is convenient in the case you have multiple section of code which use the result returned from :code:`millis()` to compute different delay intervals.


.. todo:: Blink without delay example




Exercise: Blink two LEDs at different frequencies
-------------------------------------------------

The following code demonstrates how to blink two LEDs at different frequencies using the :code:`delay()` method. It should be obvious that this code will be difficult to maintain.

.. literalinclude:: ../../../projects/platformio/timing/delay-blink-two-leds/src/main.cpp
    :language: c
    :linenos:


In this exercise a similar program should be designed without the use of :code:`delay()`.

#. Connect two external LEDs to the microcontroller.
#. Write a program which blinks one LED at a frequency of 1 Hz.
#. Extend the program to blink the other LED at a frequency of 5 Hz.

The following code listing provides one possible solution:

.. literalinclude:: ../../../projects/platformio/timing/dual-led-blink-millis/src/main.cpp
    :language: c
    :linenos:


Exercise: Adjustable blink frequency
-------------------------------------------------

In this exercise we are going to adjust the blink frequency of two LEDs independently by using two push buttons.

#. Write the necessary code to allow a push button to step through the blink frequencies 2 Hz, 1 Hz, 0.5 Hz, 0.25 Hz. After reaching the lowest frequency it should wrap around.
#. Extend the program with a second LED which should be controlled independently of the first.


The following code listing provides one possible solution. Notice how the same logic is applied twice for the two push buttons. Repetitive code usually indicate that your solution is not optimal. It will likely be possible to make the solution more generic, and applicable to an arbitrary number of inputs and outputs. We will explore this in a future lecture.

.. note:: The following solution proposal does not take the push button bouncing problem in to account. Thus you should pay attention to the output in the serial monitor, in might jump thorough several frequency steps on each button push.

.. literalinclude:: ../../../projects/platformio/timing/dual-led-blink-millis-pb-control/src/main.cpp
    :language: c
    :linenos:



Constant frequency
-------------------

.. I do not know if you like to have this here or not, Gizem :) You can move it to the L3_Additional file if you do not like to cover these details (I mean move this section and the two next sections about reducing the load, and overflow)

If constant frequency execution of your code is important, and you are certain that the code will execute in less time than your delay time, the following code can be used:

.. code-block:: c

    uint16_t time_interval = 1200; // 1200 ms delay
    uint32_t old_millis = 0;

    void loop(){

        uint32_t current_millis = millis();

        if((uint32_t)(current_millis - old_millis >= time_interval)){
            // Blink a LED, or something...
            //old_millis = millis();
            old_millis += time_interval;
        }
    }

The advantage of adding a time interval to :code:`old_millis` instead of updating it with the new millisecond value, is that the former avoid the potential problem that the delay gets off by some milliseconds if a interrupt or some other part of the :code:`loop()` function causes it to execute to late. If the code in the previous section executes two milliseconds too late on a given iteration of the loop, it will never be able to correct for this. Thus if you are implementing a watch or some other application where it is important that (on average) the timing is accurate, you should consider the approach in this section. The only source of error will be the accuracy of the clock source (typically a quarts crystal), which drives the CPU of the microcontroller.

The code in the previous section will guarantee that the delay will be at least equal to :code:`time_interval`, but it could be slightly longer.

.. todo:: More detailed explanation is needed

A problem with the approach of adding a interval on each invocation is how to set the initial condition of :code:`old_millis`. The first time :code:`millis()` is called in your software it might return a value larger than zero, because some other startup code has had the microcontroller spend some time before it enters :code:`loop()`. This will cause our delayed code to execute at a high rate until the value in :code:`old_millis` has accumulated to a value above what is returned from :code:`millis()`.

Reducing the load on the microcontroller
----------------------------------------

The CPU in the Atmega328p operates on 8 bit at a time. Hence the 32-bit operations of the previous :code:`millis()` examples requires many operations. If you only only require delays of 1 minute, or less, you can use 16 bit operations:

.. code-block:: c

    uint16_t old_millis;
    const uint16_t delay_time = 4000;
    
    uint16_t current_millis = millis();
    
    if ((current_millis - old_millis) >= delay_time){

    }


:code:`millis()` overflow
--------------------------


The counter variable used by the :code:`millis()` function is 32-bit unsigned integer. The maximum value is given by:

.. math::
    2^{32} = 4294967296

The milliseconds value can be converted to days by:

.. math::
    \frac{4294967296}{1000 \cdot 60 \cdot 60 \cdot 24} = 49.71

I.e. the function will overflow after approximately 50 days. Even if this is a long time, there are certainly applications where the microcontroller could run for much longer periods than this. For some applications this could be a problem, but for the examples demonstrated previously in this section there is no problem that the variable overflows and resets back to zero. It is however easy to fall in to the trap of the following buggy code:

.. code-block:: C

    void loop(){
        if(millis() > old_millis + delay_time){
            // Blink a LED, or something...
            old_millis = millis();
        }
    }

The above example will exhibit buggy behavior after the overflow. After the overflow the value returned by :code:`millis()` will be close to zero. The value stored in :code:`old_millis` however will be close to the number of milliseconds in 49.7 days. It is a possibility that :code:`old_millis + delay_time` will also overflow, but if it does not, it will take a very long time (49.7 days) until :code:`millis()` again become large enough for the relational operator :code:`>` to evaluate to true. Even if :code:`old_millis + delay_time` also overflows, it will still probably be a glitch here. It is very unlikely that the delay will be the same as it is supposed to be.

The question then is why this is not a problem in the code in the previous examples. We had the following comparison:

.. code-block:: C

      millis() - old_millis >= delay_time

The subtraction is evaluated before the comparison. Hence we compare the value in :code:`millis() - old_millis`, with the value in :code:`delay_time`. Since :code:`millis()` returns a unsigned value, and :code:`old_millis` is declared to be unsigned, the result from the subtraction can not become negative.

.. todo:: Add a more detailed explanation. Including explanation of modular arithmetic

Instead of thinking about the value returned from :code:`millis()` as the number of milliseconds since you restarted the microcontroller, you could also think of it as a unique time stamp of the instant that the function is called. I.e. the numeric value returned uniquely identifies the instant. Ofc these so called unique time stamps will be reused eventually after the rollover, but as long as you are not interested in time intervals of more than 49.7 days, this will not be a problem.

If the returned values are timestamps, then a duration can be computed by computing the difference between two time stamps. This duration can then be compared to the duration we want to have in our program.




Switch bouncing
===============

A mechanical switch will often generate spurious open/close transitions in a short period after it has been activated. It is a risk that these spurious transitions are interpreted as multiple signals from the switch. In order to avoid these problems some form of debounce remedy should be applied. This could be a hardware solution, a software solution or a combination of the two.

The graph to the right in the following figure illustrates the spurious changes in voltage level at the node between the resistor and the switch.
 
.. figure:: ../../../external/fig/switch_bounce.png
   :alt: Switch bounce
   :align: center

   Source: `https://www.best-microcontroller-projects.com/easy_switch_debounce.html <https://www.best-microcontroller-projects.com/easy_switch_debounce.html>`_


.. _bounce_problem_demo:

Bounce problem edge detection demonstration
-------------------------------------------

The following rising and falling edge detection software will clearly demonstrate the bouncing problem if there in fact is a bouncing problem with the mechanical switches. By observing the edge counter in a serial monitor, it will become apparent that it counts more then one event each time a button is pushed.

.. code-block:: c

    #include <Arduino.h>

    const uint8_t btn1 = 5;

    /**
     * Default the btn1_prev_state to 1 if using pull up resistors, and a push 
     * button which pulls the logic level low. Change to 0 if using pull down
     * resistors.
     * 
     * Othervise the MCU will detect a single button event upon boot.
     * 
     * Also remember that the rising edge will be at button release, if you are
     * using pull up resistors.
     */
    uint8_t btn1_prev_state = 1;

    uint8_t rise_edge_cnt = 0;
    uint8_t fall_edge_cnt = 0;

    void setup() {
      pinMode(btn1, INPUT);

      Serial.begin(9600);
    }


    void loop() {

      /**
       * Send a message on the serial port on each rising, and falling
       * edge of the push button 
       */

      uint8_t btn1_state = digitalRead(btn1);

      if(btn1_state != btn1_prev_state){
        if(HIGH == btn1_state){
          Serial.print("Rising edges detected: ");
          Serial.println(++rise_edge_cnt);
        }
        if(LOW == btn1_state){
          Serial.print("Falling edges detected: ");
          Serial.println(++fall_edge_cnt);
        }
        btn1_prev_state = btn1_state;
      }
    }

Hardware debounce methods
-------------------------

Hardware solutions include analog filters using resistors and capacitors, or digital circuits as illustrated in the following figure:

.. figure:: ../../../external/fig/Shift-register_and_a_nand_gate_as_a_debouncer.png
   :align: center

   Source: `https://www.e-tinkers.com/2021/05/the-simplest-button-debounce-solution/ <https://www.e-tinkers.com/2021/05/the-simplest-button-debounce-solution/>`_

The button state is *clocked* in to the d flip-flops, and only when all the flip-flops have registered the same state the output will change. This solution is typically found in programmable logic, but it is rather expensive to realise by using discrete components.

A really efficient and reliable debounce circuit can be built bu using a SR-latch in conjunction with a SPDT switch. In one position the switch is connected to the *set* input, while the other position of the switch is connected to *reset* input of the latch. That way you do not have to consider the time you expect the bouncing to last, or the duration between each of the spurious voltage pulses.

..
    This part is repetitive.

    Mechanical switches such as push buttons will often exhibit mechanical bouncing when they are connected. This is a relatively high frequency opening and closing of the contacts at the instant that they are supposed to close, or open. E.g. when you push a push button, the first connection instant will often be followed by several fast open close instants. Our microcontroller is more than capable of detecting these quick (but undesired) openings and closings of the switch, and may cause or software to detect multiple button events, when we only wanted one event.

    For this reason we often have to include some form of switch de-bouncing algorithm in our software. It is also possible to use a hardware solution (e.g. a SPDT switch, and an RS-latch), but in this lesson we will only consider a simple software solution. It is usually much cheaper to do the de-bouncing in software, that it is do to it in hardware.


    A simple approach is to detect the rising (or falling) edge of the digital input, and then simply ignore any changes on the input for a short duration after the first change was detected. Alternatively we could detect a change in the input, wait, and check it again to see if it is stable. More sophisticated methods are also available, and will be demonstrated in a future lecture.

    It should also be noted that it is possible to create a switch de-bouncing library which hides this details from the application. This however will also have to wait for a future lecture.


Software debounce methods
------------------------------

If a software debounce solution is desired, one possibility is to check the button state twice, within a short time windows. I.e. check, delay, check again. The following source code listing illustrates one possibility:

Note that the variables are declared :code:`static` inside the :code:`loop()` function. This ensures that the value is persistent between the invocation of the function. Alternatively they could be declared globally, i.e. outside of any function definition.


.. literalinclude:: ../../../projects//switch_debounce/delay_switch_debounce_no_func/src/main.cpp
    :language: c
    :linenos:


Switch debounce for the edge detection demo
-------------------------------------------

The following code listing demonstrates one way to solve the bouncing problem from the example in the section  :ref:`Bounce problem edge detection demonstration <bounce_problem_demo>`.

.. literalinclude:: ../../../projects/platformio/debounce/single-switch-debounce/src/main.cpp
    :language: c
    :linenos:



Exercise: Push button de-bouncing
----------------------------------

In this exercise you will (hopefully) experience the bouncing problem in practice, and then resolve the problem by applying the technique discussed in the previous section.

#. Write a program which toggles a LED on the rising edge of a push button. The program should also print a message to the serial port on the rising edge.
#. Extend the program with the code required to avoid bouncing problems.
#. Try to press the push button repetitively to make sure that it is not bouncing.



Exercise: Stopwatch
-------------------

Since we now know some ways of eliminating the glitches (or jitter) on the button, we can design a nice stopwatch timer, where a push button starts and stops the timing. Use the comments in the source code as your guide on how to solve this exercise.

.. literalinclude:: ../../../projects/StopWatch_pseudo/src/main.cpp
    :language: c
    :linenos:

.. todo:: Add solution proposal after the lecture

..
    .. literalinclude:: ../../../projects/StopWatch/src/main.cpp
    :language: c
    :linenos:


.. note:: Note that this is not necessarily a perfect solution. If you press and release the button so fast that the program cannot catch :code:`digitalRead(button_pin)` line - since the microcontroller reads memory line by line - then you miss a button press. This will likely not be a problem for manual push buttons (unless your microcontroller executes a lot of code between each time it reads the digital input), but it might be a problem if you try to use the stopwatch for timing of something which is faster than a human hand. E.g. if you want to determine the speed of you bicycle, and try to measure the interval between pulses from a sensor on the wheel. The solution for fast signals is to use the *interrupt* system. This will be covered in a future lecture.

Rough Timing in Arduino
========================

The Arduino functions associated with timing are:

- `delay() <https://www.arduino.cc/reference/en/language/functions/time/delay/>`_
- `delayMicroseconds() <https://www.arduino.cc/reference/en/language/functions/time/delaymicroseconds/>`_
- `millis() <https://www.arduino.cc/reference/en/language/functions/time/millis/>`_
- `micros() <https://www.arduino.cc/reference/en/language/functions/time/micros/>`_



