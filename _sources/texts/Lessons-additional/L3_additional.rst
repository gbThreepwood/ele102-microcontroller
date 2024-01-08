*******************************************************
Additional material for lesson 3
*******************************************************

.. _conditionals_easy:

Simple touch to If...Else
===========================

.. seealso::

    Very nice exercises about conditionals `here. <https://www.w3resource.com/c-programming-exercises/conditional-statement/index.php>`_


Why choosing if...else wisely is important.

.. image:: ../../../external/fig/if_else_3.jpg
        :alt: if...else problem
        :align: center

.. image:: ../../../external/fig/if_else_2.jpg
        :alt: if...else BAD
        :align: center

.. image:: ../../../external/fig/if_else_1.jpg
        :alt: if...else BETTER
        :align: center


Switch debounce methods
=================================

Switch bounce is the mechanical bouncing of the contacts in an electrical switch, causing what should be a single event to appear to be multiple events (e.g. multiple presses of a push button). It is a feature of most electical swiches, and causes problems when the switches are interfaced with fast digital electronics, such as microcontrollers. Many solutions have been developed to overcome these issues, and thus it is impossible to cover all of them. This chapter aims to provide you with some effective hardware and software solutions.

.. warning:: The part about switch debounce is not ready.

Measuring switch bounce using oscilloscope
------------------------------------------


Software solutions
------------------

Switch debounce in hardware is a effective solution, but additional hardware increases the cost and thus it is often possible to save some cost by implementing software debounce. Switch change may be detected by polling the state of the digital input, or the input may generate an interrupt to the CPU. Regardless of which metod one is using, care must be taken to not detect several push events because of bouncing.

Polling is often the preferred method to detect push buttons, although constant polling does waste some CPU cycles. In applications such as battery operated devices, the microcontroller could be put into a sleep state in order to save power when it is not in use. A interrupt is often required to wake up the controller, but afterwards in could enter a polling mode for the inputs. In simple applications the polling could be acheived in the main loop, or by using a timer interrupt where the ISR polls the inputs. If the controller is running a operating system, it could also be possible to use a thread for the polling.

The interrupt solution requires extra attention because the bouncing in some cases may cause the ISR to fire many times within a short time period. This can have a bad impact on the performance of the microcontroller. The solution will often be that the pin change interrupt is temporary disabled by the ISR on the first invocation. After some delay the interrupt is re-enabled.

Because many applications require debouncing of more than one push button, it is often useful to create a library which provides a consistant way of adding debounce to the inputs that require this.


In this section we will look at more sophisticated switch debounce techniques.

The following function allows de-bouncing up to 8 digital inputs simultaneously:

.. code-block:: c

    uint8_t switch_debounce(uint8_t current_state){

        static uint8_t asserted = 0x00;
        static uint8_t previous = 0x00;

        asserted |= (previous & current_state);
        asserted &= (previous | current_state);

        previous = current_state;

        return asserted;
    }




..  The following function allows de-bouncing up to 8 digital inputs simultaneously:

    .. code-block:: c

        uint8_t switch_debounce(uint8_t current_state){

            static uint8_t asserted = 0x00;
            static uint8_t previous = 0x00;

            asserted |= (previous & current_state);
            asserted &= (previous | current_state);

            previous = current_state;

            return asserted;
        }



Measure the bouncing using interrupt
------------------------------------

A oscilloscope is a good tool for evaluating the bouncing problemens of a push button. If you do not have access to a oscilloscope, it is also possible to use the controller itself to measure the bouncing. This method is less accurate, because the voltage levels during the bouncing period may vary, and only occationally cause spurious detection of button events.

The following example is using a pin change interrupt to count the bouncing of the signal on a digital input. Note that the ISR fires on both rising and falling edge of the signal, thus you will at least trigger the ISR once on button push, and once on button release.

.. literalinclude:: ../../../projects/interrupt/pin_change_switch_bounce_counter/src/main.cpp
  :language: c



Delay method
------------

The following method checks for a rising edge on the digital input, and subsequently ignores any change of state for a given delay period. A important issue with this method is that the required delay period will vary depending on the type of switch that i used.

.. literalinclude:: ../../../projects/switch_debounce/delay_switch_debounce/src/main.cpp
  :language: c

Shift register method
---------------------

The following example is intended to demonstrate the operation of the shift register debounce method, by printing the state of the shift register to the UART. The shift register method is in some ways superior to the delay method, because it will detect if the input is stable rather than simply detecting a edge, and ignoring the input for some period. The sampling delay for the shift register method may be much shorter than the delay for the delay method.

.. literalinclude:: ../../../projects/switch_debounce/shift_register_switch_debounce/src/main.cpp
  :language: c


Using a timer interrupt to poll the inputs
-------------------------------------------

The following example shows one way to poll three digital inputs using a timer interrupt. The :code:`get_button_event(push_button_t button)` function is included in order to abstract away the operation of the button event detect algorithm. This is a more complete example of how to use debouncing in an application, but it could certainly be improved. In particular the operation of the switch debounce could be hidden in a separate file (a library), and the events could be posted to a queue.

.. literalinclude:: ../../../projects/switch_debounce/timer_polling_switch_debounce/src/main.cpp
  :language: c

Pin change interrupt debounce with smoothing capacitor
------------------------------------------------------

.. warning:: You should normally avoid the pin change interrupts for push buttons, unless you are absolutely sure that you need it. The aforementioned methods are simpler to implement, and more general in nature. The pin change interrupt is a specific feature of some of the AVR controllers.
  
  The problem with using interrupts with switch inputs is that bouncing will cause the ISR to fire multiple times, unless the ISR disables the interrupt on the first invocation. This complicates things, although it is certainly possible to do.


Pin change interrupt debounce software only
-------------------------------------------

In this section we are demonstrating one way of detecting and debouncing inputs using pin change interrupts. The following algorithm is used:

.. uml::

  (*) --> "ISR fires on one of the inputs"
  --> "Check wich input caused the interrupt"
  --> "Set flag, or post message"
  --> "Disable the pin change interrupt"
  --> (*)

.. uml::

  (*) --> "Main loop or timer checks for new events"
  If "New event" then
  --> [Yes] "Store"
  else
  --> (*)
  Endif

  
.. literalinclude:: ../../../projects/interrupt/pin_change_interrupt_switch_debounce/src/main.cpp
  :language: c

Creating a library for software debounce
----------------------------------------





Multi tasking on the microcontroller
====================================


Although we are using the :code:`delay()` function in almost everywhere, it is a very dangerous function. It halts any processing completely. No reading of sensors, mathematical calculations, or pin manipulation can go on during delay function.

    
**Exercise for home:** Modify the interrupt code without using delay functions. There is a very nice project using an LCD in `this link <https://www.youtube.com/watch?v=TD-J7LgrsBQ>`_.


No more delay()
-----------------
Using delay() to control timing is probably one of the very first things you learned when experimenting with the Arduino. Timing with delay() is simple and straightforward, but it does cause problems down the road when you want to add additional functionality. The problem is that delay() is a "busy wait" that monopolizes the processor. 

During a delay() call, you can’t respond to inputs, you can't process any data and you can’t change any outputs. The delay() ties up 100% of the processor.  So, if any part of your code uses a delay(), everything else is dead in the water for the duration.

Blink without delay:

.. code-block:: c

    /*
     http://www.arduino.cc/en/Tutorial/BlinkWithoutDelay
    */

    // constants won't change. Used here to 
    // set pin numbers:
    const int ledPin =  13;      // the number of the LED pin

    // Variables will change:
    int ledState = LOW;             // ledState used to set the LED
    long previousMillis = 0;        // will store last time LED was updated

    // the follow variables is a long because the time, measured in miliseconds,
    // will quickly become a bigger number than can be stored in an int.
    long interval = 1000;           // interval at which to blink (milliseconds)

    void setup() {
    // set the digital pin as output:
    pinMode(ledPin, OUTPUT);      
    }

    void loop()
    {
    // here is where you'd put code that needs to be running all the time.

    // check to see if it's time to blink the LED; that is, if the 
    // difference between the current time and last time you blinked 
    // the LED is bigger than the interval at which you want to 
    // blink the LED.
    unsigned long currentMillis = millis();
    
    if(currentMillis - previousMillis > interval) {
        // save the last time you blinked the LED 
        previousMillis = currentMillis;   

        // if the LED is off turn it on and vice-versa:
        if (ledState == LOW)
        ledState = HIGH;
        else
        ledState = LOW;

        // set the LED with the ledState of the variable:
        digitalWrite(ledPin, ledState);
    }
    }


Blink without delay (extended):

.. code-block:: c

    // These variables store the flash pattern
    // and the current state of the LED

    int ledPin =  13;      // the number of the LED pin
    int ledState = LOW;             // ledState used to set the LED
    unsigned long previousMillis = 0;        // will store last time LED was updated
    long OnTime = 250;           // milliseconds of on-time
    long OffTime = 750;          // milliseconds of off-time

    void setup() 
    {
    // set the digital pin as output:
    pinMode(ledPin, OUTPUT);      
    }

    void loop()
    {
    // check to see if it's time to change the state of the LED
    unsigned long currentMillis = millis();
    
    if((ledState == HIGH) && (currentMillis - previousMillis >= OnTime))
    {
        ledState = LOW;  // Turn it off
        previousMillis = currentMillis;  // Remember the time
        digitalWrite(ledPin, ledState);  // Update the actual LED
    }
    else if ((ledState == LOW) && (currentMillis - previousMillis >= OffTime))
    {
        ledState = HIGH;  // turn it on
        previousMillis = currentMillis;   // Remember the time
        digitalWrite(ledPin, ledState);	  // Update the actual LED
    }
    }

