:orphan:

*******************************************************
Digital I/O, and serial port (Cont'd)
*******************************************************

In this lesson we will practice what we learned about last lecture on digital I/O, serial monitor interaction.


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



Hands-on Exercises
====================
Time to use expand the knowledge by doing!


Simple button press counter
----------------------------

Imagine that you develop a system that counts how many times a button pressed. It can be on a secure door, a keyboard software or a simple knitting row counter! Here is a simple system: 

.. figure:: ../../../external/fig/simple_button_press.png
   :align: center


Here is the code:

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


.. note:: Note that how different serial print methods are used.

Can you run and define if the system meets your requirements or something is off? What if you change the :code:`delay()` duration? Would it be *perfect* then? What if you move :code:`uint16_t count = 0;` in a function, let say in :code:`setup()` or :code:`loop()`. Play with this code and define the problem please.


Simple button LED state change
-------------------------------

Maybe we can modify the code such that the LED is not *directly* controlled by the button value but it can be controlled by its *change*. Design a system that changes the LED state in every button press.

.. code-block:: c

    const uint8_t led_pin = 2;
    const uint8_t button_pin = 11;
    bool button_state_now = 0;
    bool button_state_prev = 0;
    bool led_state = 0;

    void setup(){
        pinMode(led_pin, OUTPUT);
        pinMode(button_pin, INPUT);
    }

    void loop (){
        button_state_now = digitalRead(button_pin);
        if(button_state_now != button_state_prev){
            led_state = !led_state;
        }
        digitalWrite(led_pin, led_state);
        delay(100);
    }



Debounce of mechanical switch
------------------------------

A mechanical switch will often generate spurious open/close transitions in a short period after it has been activated. It is a risk that these spurious transitions are interpreted as multiple signals from the switch. In order to avoid these problems some form of debounce remedy should be applied. This could be a hardware solution, a software solution or a combination of the two.

.. figure:: ../../../external/fig/switch_bounce.png
   :align: center

   Source: `https://www.best-microcontroller-projects.com/easy_switch_debounce.html <https://www.best-microcontroller-projects.com/easy_switch_debounce.html>`_

If a software solution is desired, one possibility is to check the button state twice, within a short time windows. I.e. check, delay, check again.


.. literalinclude:: ../../../projects//switch_debounce/delay_switch_debounce/src/main.cpp
    :language: c
    :linenos:



Hardware solutions include analog filters using resitors and capacitors, as well as using a SR-latch.


.. figure:: ../../../external/fig/Shift-register_and_a_nand_gate_as_a_debouncer.png
   :align: center

   Source: `https://www.e-tinkers.com/2021/05/the-simplest-button-debounce-solution/ <https://www.e-tinkers.com/2021/05/the-simplest-button-debounce-solution/>`_



..
    This part is repetitive.

    Mechanical switches such as push buttons will often exhibit mechanical bouncing when they are connected. This is a relatively high frequency opening and closing of the contacts at the instant that they are supposed to close, or open. E.g. when you push a push button, the first connection instant will often be followed by several fast open close instants. Our microcontroller is more than capable of detecting these quick (but undesired) openings and closings of the switch, and may cause or software to detect multiple button events, when we only wanted one event.

    For this reason we often have to include some form of switch de-bouncing algorithm in our software. It is also possible to use a hardware solution (e.g. a SPDT switch, and an RS-latch), but in this lesson we will only consider a simple software solution. It is usually much cheaper to do the de-bouncing in software, that it is do to it in hardware.


    A simple approach is to detect the rising (or falling) edge of the digital input, and then simply ignore any changes on the input for a short duration after the first change was detected. Alternatively we could detect a change in the input, wait, and check it again to see if it is stable. More sophisticated methods are also available, and will be demonstrated in a future lecture.

    It should also be noted that it is possible to create a switch de-bouncing library which hides this details from the application. This however will also have to wait for a future lecture.


The following function allows de-bouncing up to 8 digital inputs simultanously 

.. code-block:: c

    uint8_t switch_debounce(uint8_t current_state){

        static uint8_t asserted = 0x00;
        static uint8_t previous = 0x00;

        asserted |= (previous & current_state);
        asserted &= (previous | current_state);

        previous = current_state;

        return asserted;
    }


Stopwatch
-----------
Since we know some ways of eliminating the glitches (or jitter) on the button, we can design a nice stopwatch timer.

.. literalinclude:: ../../../projects/StopWatch/src/main.cpp
    :language: c
    :linenos:


.. note:: Note that this is not a perfect solution either. If you press and release the button so fast that the program cannot catch :code:`digitalRead(button_pin)` line - since the microcontroller reads memory line by line - then you miss a button press. You need to *interrupt* your system. Yes, we will do it later :)



Triple button, triple LED
---------------------------

.. todo:: Start with regular long way with 2 buttons+2LED first.

With arrays, a better way:

.. code-block:: c

    const int inputPins[] = {2,3,4};
    const int ledPins[] = {9,10,11};
    void setup(){
        for(int i = 0; i < 3; i++){
            pinMode(ledPins[i], OUTPUT);
            delay(1);
            pinMode(inputPins[i], INPUT);
            delay(1);
        }
        Serial.begin(9600);
    }
    void loop(){
        for(int i = 0; i < 3; i++){
            int val = digitalRead(inputPins[i]);
            if (val == LOW){
                digitalWrite(ledPins[i], HIGH);
            }
            else{
                digitalWrite(ledPins[i], LOW);
            }
        }
    }   


Serial UI for LED control
-------------------------
Let's design a serial UI for controlling the button state.


.. literalinclude:: ../../../projects/led_serial/led_serial.ino
    :language: c
    :linenos:





Switch bouncing
===============

A mechanical switch will often generate spurious open/close transitions in a short period after it has been activated. It is a risk that these spurious transitions are interpreted as multiple signals from the switch. In order to avoid these problems some form of debounce remedy should be applied. This could be a hardware solution, a software solution or a combination of the two.

The graph to the right in the following figure illustrates the spurious changes in voltage level at the node between the resistor and the switch.
 
.. 
   figure:: ../../../external/fig/switch_bounce.png
   :alt: Switch bounce
   :align: center
    
    Source: `https://www.best-microcontroller-projects.com/easy_switch_debounce.html <https://www.best-microcontroller-projects.com/easy_switch_debounce.html>`_


Debounce methods
------------------------------

If a software debounce solution is desired, one possibility is to check the button state twice, within a short time windows. I.e. check, delay, check again. The following source code listing illustrates one possibility:

.. literalinclude:: ../../../projects//switch_debounce/delay_switch_debounce_no_func/src/main.cpp
    :language: c
    :linenos:

Hardware solutions include analog filters using resistors and capacitors, or digital circuits as illustrated in the following figure:

.. 
   figure:: ../../../external/fig/Shift-register_and_a_nand_gate_as_a_debouncer.png
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

Exercise: Push button de-bouncing
----------------------------------

#. Write a program which toggles a LED on the rising edge of a push button. The program should also print a message to the serial port on the rising edge.
#. Extend the program with the code required to avoid bouncing problems.
#. Try to press the push button repetitively to make sure that it is not bouncing.



Exercise: Stopwatch
-------------------

Since we know some ways of eliminating the glitches (or jitter) on the button, we can design a nice stopwatch timer.

.. literalinclude:: ../../../projects/StopWatch_pseudo/src/main.cpp
    :language: c
    :linenos:


.. note:: Note that this is not a perfect solution either. If you press and release the button so fast that the program cannot catch :code:`digitalRead(button_pin)` line - since the microcontroller reads memory line by line - then you miss a button press. You need to *interrupt* your system. Yes, we will do it later :)



.. According to our new plan array will be discussed in lecture 6.

    Exercise: Triple button, triple LED
    -----------------------------------

    .. todo:: Start with regular long way with 2 buttons+2LED first.

    .. When you have similar identities to be defined, changed, manipulated etc. you can use **arrays**. Here you have 3 button and 3 LED requested. With arrays, a better way:

    .. code-block:: c

        const int inputPins[] = {2,3,4};
        const int ledPins[] = {9,10,11};
        void setup(){
            for(int i = 0; i < 3; i++){
                pinMode(ledPins[i], OUTPUT);
                delay(1);
                pinMode(inputPins[i], INPUT);
                delay(1);
            }
            Serial.begin(9600);
        }
        void loop(){
            for(int i = 0; i < 3; i++){
                int val = digitalRead(inputPins[i]);
                if (val == LOW){
                    digitalWrite(ledPins[i], HIGH);
                }
                else{
                    digitalWrite(ledPins[i], LOW);
                }
            }
        }   

..  Also according to our plan serial input will come in lecture 6

    Exercise: Serial UI for LED control
    =====================================

    Let's design a serial UI for controlling the button state.

    Design requirements:

    - An LED is connected to pin number 3 will be controlled by keyboard inputs.
    - The user is allowed to enter **1**, **2** and **3** as input. If something else is entered, a help manual will appear.
    - Keyboard number 1 will turn on the LED.
    - Keyboard number 2 will turn off the LED.
    - Keyboard number 3 will give the help manual as shown in the figure.

    .. figure:: ../../../external/fig/led_serial_requirements.png
       :align: center


    .. note:: The :code:`rx_byte = Serial.read();` will be useful.


    .. 
        Solution:

        .. literalinclude:: ../../../projects/led_serial/led_serial.ino
            :language: c
            :linenos:


