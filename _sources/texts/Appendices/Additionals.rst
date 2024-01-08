.. _Additionals:

***********************************
Additional Chapters
***********************************

4-bit binary counter
--------------------
.. warning::
        This example is somewhat advanced, and you should not worry if you are unable to follow all the details. They will become clear as the course progresses.

In order to demonstrate how the digital output really works we are goint to create a 4-bit binary counter. First we will use the Arduino function to access the digital outputs, and afterwards we will access the output register directly, in order to demonstrate how this can optimize our program.

Start by building the following circuit. In order to comply with the upcoming example code, you should use the same digital output pins, as shown in the figure.

.. figure:: ../../fig/4_led_bb.png
        :align: center
        :scale: 50

The following program counts the LED's in the binary sequence from 0 to 15. Each call to `digitalWrite` only updates one output, and thus we use several conditional sentences to make sure the functions are invoked at the correct time. In addition the program prints the status of the counter to the serial port.

.. code-block:: c

        #include <Arduino.h>
        
        void setup() {
          // put your setup code here, to run once:
          pinMode(4,OUTPUT);
          pinMode(5,OUTPUT);
          pinMode(6,OUTPUT);
          pinMode(7,OUTPUT);
        
          Serial.begin(9600);
        
        }
        
        void loop() {
          // put your main code here, to run repeatedly:
        
          for(int i = 0; i < 16; i++){
        
            Serial.write("Tallet er: ");
            Serial.print(i);
            Serial.write('\n');
        
            if(i%2)
              digitalWrite(4,HIGH);
            else
              digitalWrite(4,LOW);
        
        
            if((i/2)%2)
              digitalWrite(5,HIGH);
            else
              digitalWrite(5,LOW);
        
        
            if((i/4)%2)
              digitalWrite(6,HIGH);
            else
              digitalWrite(6,LOW);
        
        
            if((i/8)%2)
              digitalWrite(7,HIGH);
            else
              digitalWrite(7,LOW);
        
            delay(1000);
          }
        
        }


Computers work with binary numbers, and thus one might be suprised about the complexity of the code involved just to make the computer count from 0 to 15, and indeed there is a simpler solution.

The problem arrises from the `digitalWrite` function, restricting our access to only one output at a time. Internally though, at a lower level, the outputs are contained in 8-bit registers, and a single CPU instruction is capable of modifying 8 outputs. If we can figure out which register to modify, we could simply increment the value of this register by one, from 0 to 15 to achieve the same result. Direct register access will make your code less portable and readable, and thus it should be used with causion.

.. note:: A experienced programmer would typically write some kind of wrapper to isolate as much as possible of the logical code, from the code that directly interacts with hardware. That way only the direct hardware access code must be changed if porting to a different microcontroller.

By looking at the Arduino UNO pinout, we see that the pins we happen to be using (pin 4 - 7) are connected to port PD4 to PD7 in the microcontroller. The following code configures these pins as outputs, and sets PD7, and PD5 high, illuminating the two LED's.

.. code-block:: c

        #include <Arduino.h>
        
        void setup() {
          // put your setup code here, to run once:
        
          DDRD = 0b11110000; // Configure PD4 - PD7 as outputs, the rest as inputs.
        }
        
        void loop() {
          // put your main code here, to run repeatedly:
        
          PORTD = 0b10100000;
        }
       
The counter may be implemented by incrementing the 4 most significant bits of the `PORTD` register from 0 to 15. This is achieved by shifthing the value in the counter variable `i` 4 places to the left using the bit-shift operator `<<`.

.. code-block:: c

        #include <Arduino.h>

        void setup() {
          // put your setup code here, to run once:
        
          DDRD = 0b11110000; // Configure PD4 - PD7 as outputs, the rest as inputs.
        }
        
        void loop() {
          // put your main code here, to run repeatedly:
        
          for(int i = 0; i < 16; i++){
            PORTD =  (i << 4);
            delay(1000);
          }
        }


.. note:: As a simple exercise you could try to extend the program to a 8-bit counter. In that case you might want to reduce the time delay.

        Creating a counter with more than 8 bit introduce an additional challenge because you have to manipulate more than one output register. It is however rather straightforward once you know how to do bitwise manipulations. 


Using a potentiometer to test the analog to digital converter
-------------------------------------------------------------

.. warning::
        This is a rather complex example, and is mostly intended as a demonstration. It is not expected that you will be able to follow all the details of the code this early in the course.

In this example we will be using a potentiometer to apply a varying voltage to the ADC of the Arduino. The conversion result will be fed back to us using the serial UART. In addition a bar of 8 LED's will be used to indicate the voltage level. The software supports two modes for the LED display, a dot-mode, and a bar-mode.

.. figure:: ../../fig/potmeter_reading_bb.png
        :align: center
        :scale: 50


.. warning::
        This code could (should?) be improved.

.. code-block:: c
        
        #include <Arduino.h>
        
        enum State_enum {BAR_STATE, DOT_STATE};
        
        void led_bar(int);
        void led_dot(int);
        int buttonPressed(uint8_t);
        
        
        void setup() {
          // put your setup code here, to run once:
        
          pinMode(2,OUTPUT);
          pinMode(3,OUTPUT);
          pinMode(4,OUTPUT);
          pinMode(5,OUTPUT);
          pinMode(6,OUTPUT);
          pinMode(7,OUTPUT);
          pinMode(8,OUTPUT);
          pinMode(9,OUTPUT);
        
          pinMode(10,INPUT);
        
          Serial.begin(9600);
        
        }
        
        
        void loop() {
        
          // put your main code here, to run repeatedly:
          int adc_reading = 0;
          float voltage = 0;
        
          unsigned long past = 0;
          uint8_t state = BAR_STATE;
        
          for(;;){
        
            delay(100);
            adc_reading = analogRead(0);
            voltage = adc_reading*0.00489;
        
        
            /*
             * Only write to the serial port once every second.
             */
            if((millis() - past) > 1000){
              past = millis();
        
              Serial.print("ADC verdi: ");
              Serial.print(adc_reading);
              Serial.print('\n');
              Serial.print("Spenning: ");
              Serial.print(voltage);
              Serial.print('\n');
            }
        
            switch (state)
            {
            case BAR_STATE:
        
              if(buttonPressed(10))
                state = DOT_STATE;
        
              led_bar(adc_reading);
              break;
        
            case DOT_STATE:
        
              if(buttonPressed(10))
                state = BAR_STATE;
        
              led_dot(adc_reading);
              break;
        
            default:
              break;
            }
        
          }
        }
        
        void led_bar(int adc_reading){
           /*
            * LED is connected to PD2 - PD7 and PB0 - PB1.
            */
           if(adc_reading > 128)
             digitalWrite(2,HIGH);
           else
             digitalWrite(2,LOW);
           if(adc_reading > 256)
             digitalWrite(3,HIGH);
           else
             digitalWrite(3,LOW);
           if(adc_reading > 384)
             digitalWrite(4,HIGH);
           else
             digitalWrite(4,LOW);
           if(adc_reading > 512)
             digitalWrite(5,HIGH);
           else
             digitalWrite(5,LOW);
           if(adc_reading > 640)
             digitalWrite(6,HIGH);
           else
             digitalWrite(6,LOW);
           if(adc_reading > 768)
             digitalWrite(7,HIGH);
           else
             digitalWrite(7,LOW);
           if(adc_reading > 896)
             digitalWrite(8,HIGH);
           else
             digitalWrite(8,LOW);
           if(adc_reading >= 1023)
             digitalWrite(9,HIGH);
           else
             digitalWrite(9,LOW);
        }
        
        void led_dot(int adc_reading){
        
          if((adc_reading > 0) && (adc_reading < 128))
            digitalWrite(2,HIGH);
          else
            digitalWrite(2,LOW);
        
          if((adc_reading >= 128) && (adc_reading < 240))
            digitalWrite(3,HIGH);
          else
            digitalWrite(3,LOW);
        
          if((adc_reading >= 240) && (adc_reading < 351))
            digitalWrite(4,HIGH);
          else
            digitalWrite(4,LOW);
        
          if((adc_reading >= 351) && (adc_reading < 462))
            digitalWrite(5,HIGH);
          else
            digitalWrite(5,LOW);
        
          if((adc_reading >= 462) && (adc_reading < 573))
             digitalWrite(6,HIGH);
           else
             digitalWrite(6,LOW);
         
          if((adc_reading >= 573) && (adc_reading < 648))
             digitalWrite(7,HIGH);
           else
             digitalWrite(7,LOW);
         
          if((adc_reading >= 648) && (adc_reading < 795))
             digitalWrite(8,HIGH);
           else
             digitalWrite(8,LOW);
         
          if(adc_reading >= 795)
             digitalWrite(9,HIGH);
           else
             digitalWrite(9,LOW);
         
         }
        
        /*
         * Check if button is pressed, i.e. if it is high, and it was low before.
         */
        int buttonPressed(uint8_t digital_input) {
        
          static uint16_t lastStates = 0; // Store the states of digital input 0 - 15.
        
          uint8_t state = digitalRead(digital_input);
        
          if (state != ((lastStates >> digital_input) & 1)) {
            lastStates ^= 1 << digital_input;
            return state == HIGH;
          }
          return false;
        }


Debounce of mechanical switch
-------------------------------

A mechanical switch will often generate spurious open/close transitions in a short period after it has been activated. It is a risk that these spurious transitions are interpreted as multiple signals from the switch. In order to avoid these problems some form of debounce remedy should be applied. This could be a hardware solution, a software solution or a combination of the two.

If a software solution is desired, one possibility is to check the button state twice, within a short time windows. I.e. check, delay, check again.

Hardware solutions include analog filters using resitors and capacitors, as well as using a SR-latch.

Here the only function that you need to know for debugging your code is :code:`Serial.print()`. With this function, you can see some text on your *Serial Monitor*. The simplest setup to do it is as it shown:

.. code-block:: c
   :caption: Simple Serial

    void setup() {
        Serial.begin(9600); // open the serial port at 9600 bps
    }

    void loop() {
        Serial.println("Hello world"); // text written what you want to see      
        delay(500); // delay 0.5 seconds before the sending
    }


Let's write a program that takes the button state *detecting edges* both rising and falling, and write it on your serial monitor [#f1]_ .

First, let's connect the button:

.. figure:: ../../../external/fig/buttonconnect_schematic.png
   :align: center

This will look like that with your breadboard and Arduino:

.. figure:: ../../../external/fig/buttonconnect.png
   :align: center







Generalizing the concept
~~~~~~~~~~~~~~~~~~~~~~~~

The following example depicts a useful function for rising edge detection of multiple inputs.

.. code-block:: c

        
        /*
         * Check if button is pressed, i.e. if it is high, and it was low before.
         */
        int buttonPressed(uint8_t digital_input) {
        
          static uint16_t lastStates = 0; // Store the states of digital input 0 - 15.
        
          uint8_t state = digitalRead(digital_input);
        
          if (state != ((lastStates >> digital_input) & 1)) {
            lastStates ^= 1 << digital_input;
            return state == HIGH;
          }
          return false;
        }



        
Rough Timing in Arduino
========================

The Arduino functions associated with timing that we will be using in this tutorial are:

- `delay() <https://www.arduino.cc/reference/en/language/functions/time/delay/>`_
- `delayMicroseconds() <https://www.arduino.cc/reference/en/language/functions/time/delaymicroseconds/>`_
- `millis() <https://www.arduino.cc/reference/en/language/functions/time/millis/>`_
- `micros() <https://www.arduino.cc/reference/en/language/functions/time/micros/>`_

Operation
---------

The Atmega 328 has several built in hardware timers (two 8-bit and one 16-bit). These timers allow for many advanced timing applications. The Arduino library utilizes one of these timers for a counter that counts the number of microseconds since the last time the controller was restarted. Several functions are available for utilization of this counter value.

The `millis()` function returns the the number of milliseconds since the last time the controller was restarted. It is using a 32-bit unsigned integer to store the counter value, thus the maximum value is given by:

.. math::
        2^{32} = 4294967296

If we convert this value from milliseconds, to days we get:

.. math::
        \frac{4294967296}{1000 \cdot 60 \cdot 60 \cdot 24} = 49.7

Thus we see that the counter will wrap around after approximately 50 days. This should be accounted for in applications where the controller is running continously for extended periods of time.

The following code is excerpt from the Arduino library, and shows the implementation of the `millis()` function:

.. code-block:: c

        unsigned long millis()
        {
        	unsigned long m;
        	uint8_t oldSREG = SREG;
        
        	// disable interrupts while we read timer0_millis or we might get an
        	// inconsistent value (e.g. in the middle of a write to timer0_millis)
        	cli();
        	m = timer0_millis;
        	SREG = oldSREG;
        
        	return m;
        }
        
If higher accuracy is required there is also a `micros()` function, returning the number of microseconds since the last reboot.

The following code depicts the implementation of the `micros()` function:

.. code-block:: c

        unsigned long micros() {
        	unsigned long m;
        	uint8_t oldSREG = SREG, t;
        
        	cli();
        	m = timer0_overflow_count;
        #if defined(TCNT0)
        	t = TCNT0;
        #elif defined(TCNT0L)
        	t = TCNT0L;
        #else
        	#error TIMER 0 not defined
        #endif
        
        #ifdef TIFR0
        	if ((TIFR0 & _BV(TOV0)) && (t < 255))
        		m++;
        #else
        	if ((TIFR & _BV(TOV0)) && (t < 255))
        		m++;
        #endif
        
        	SREG = oldSREG;
        
        	return ((m << 8) + t) * (64 / clockCyclesPerMicrosecond());
        }


In embedded systems it is often required to write code that has some delay between executing different parts of the code. For very simple applications it might be sufficient to use code that simply blocks until a given amount of time has passed. The `delay()` function will block for the given number of milliseconds, before execution continues.

The `delay()` function is implemented as a so called waste routine. It wastes execution cycles while waiting for a counter to reach zero.

.. code-block:: c
       
        void delay(unsigned long ms)
        {
        	uint32_t start = micros();
        
        	while (ms > 0) {
        		yield();
        		while ( ms > 0 && (micros() - start) >= 1000) {
        			ms--;
        			start += 1000;
        		}
        	}
        }
        

If finer control over the delay is needed, the `delayMicroseconds()` function may be used. The code for this function is to involved to be included here, but it is available it the `official repository for Arduino <https://github.com/arduino/ArduinoCore-avr/blob/master/cores/arduino/wiring.c>`_

Usage
-----

We have allready seen how the `delay()` function is used in the simple LED blink example. The `millis()` function allows us to do more interesting and useful delay implementations, where different parts of the code may execute while we are waiting for the required time to pass.


Stopwatch
---------

.. figure:: ../../fig/digital_input_and_output_for_stop_watch_bb.png
        :align: center
        :scale: 50

.. literalinclude:: ../../../projects/StopWatch/src/main.cpp 
   :language: c

Assignment 1 : Real time clock
------------------------------

.. code-block:: c
        
        void setup()
        {
           Serial.begin(9600);
        }
        
        //globale variable
        int Year=2019;
        int Month=1;
        int Date=2;
        int Hour=12;
        int Minutes = 0;
        int Seconds = 0;
        
        void loop()
        {
           //oppdater tidsvarable
           Seconds++; //kompakt versjon av Second = Second + 1;
           if (Seconds == 60)
           {
              Seconds = 0;
              Minutes++;
           }
        
           // Skriv ut verdier
           Serial.print(Hour);
           Serial.print(":");
           Serial.print(Minutes);
           Serial.print(":");
           Serial.println(Seconds);
        
           // vent
           delay(1000); // vent et sekund
        }
        


Switching frequency modulation example
--------------------------------------

.. figure:: ../../fig/dc_motor_drive_potmeter_control_three_button_bb.png
    :scale: 50
    :align: center


The following source code listing is the complete software for the motor drive with switching frequency control:


.. container:: toggle

    .. container:: header

        **Show/Hide Code**

    .. literalinclude:: ../../../projects/dc_motor_drive_swfreq_mod/src/main.cpp
       :language: c

Arduino math functions
----------------------

The Arduino library comes with several useful math functions. Some of these are almost self explanatory, while others may require some explaning. As always the best place to start looking for information about the Arduino built in functions is the `Arduino language reference <https://www.arduino.cc/reference/en/>`_ 

* abs()
* constrain()
* map()
* max()
* min()
* pow()
* sq()
* sqrt()

Trigonometry
~~~~~~~~~~~~

* cos()
* sin()
* tan()

If you need more advanced math functions the first library to check is `math.h <http://www.nongnu.org/avr-libc/user-manual/group__avr__math.html>`_


Simple motor drive example with speed control
----------------------------------------------

The function :code:`analogWrite()` will be used to generate a PWM signal to the transistor, and the switching frequency will stay at it's default value. A analog signal input on `A0` will be used to control the duty cycle of the PWM, and hence the torque (and consequently the speed) of the motor.

.. figure:: ../../fig/dc_motor_drive_single_transistor_speed_control_bb.png
    :scale: 50
    :align: center


.. literalinclude:: ../../../projects/dc_motor_drive_single_transistor_speed_control/src/main.cpp
    :language: c


.. rubric:: Footnotes

.. [#f1] Source code as arduino.cc `DigitalReadSerial <https://www.arduino.cc/en/Tutorial/DigitalReadSerial>`_


