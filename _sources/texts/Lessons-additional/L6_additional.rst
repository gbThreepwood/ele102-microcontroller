*******************************************************
Additional material for lesson 6
*******************************************************


Serial terminal emulator software
=================================

Both the standard Arduino IDE, and Visual studio code (with PlatformIO) has built in serial terminal emulators. If however you require more advanced features, different software might be more appropriate.

Minicom is a popular GNU/Linux serial terminal. Refer to the manual for your distribution for instructions on how to install it.

PuTTY is a popular alternative for Windows. You may download it at `putty.org <https://www.putty.org/>`_.


Store the strings in program memory
-----------------------------------

By default the strings you use in your writes to the serial port are stored in RAM. The SRAM in Atmega328P is only 2 kB. If you have lots of text that you wish to send to the serial port, this can quickly consume all of your memory.

To overcome this issue you may store the strings in program memory. There are however some implications to consider.

This fact may seem a bit confusing to anyone that is new to microcontrollers. After all the SRAM is volatile, all it's content is deleted when you reset or power off the microcontroller. Then how can the strings survive the power cycle? The explanation for this is that the strings are stored in program memory, and copied into SRAM during the boot process of the controller. In other words they occupy the same amount of memory in both SRAM, and flash ROM.

This is required because the CPU instruction to read RAM differ from the function (LPM) that read the flash ROM. Any function that supports normal variables will fail to access data from ROM.

You may read more about the various memories in Arduino `here <https://playground.arduino.cc/Learning/Memory/>`_

In order to tell the compiler to store data in program memory you may use the `PROGMEM` modifier:

.. code-block:: c

        const PROGMEM uint16_t testData[] = { 65535, 32000, 12000, 20, 12345};



The following example illustrates how to store and retrieve some numbers, and one string from program memory:

.. literalinclude:: ../../../projects/serial_write_string_from_progmem/src/main.cpp
        :language: c


If all you need is to write a string to the serial port, and you don't want to waste memory the `F()` macro may be used:

.. code-block:: c

        Serial.print(F("This string is constant, thus it is best to store it in FLASH"));


You may read more about how to use the program memory to store data in the `Arduino reference <https://www.arduino.cc/reference/tr/language/variables/utilities/progmem/>`_

Simple command line interface
-----------------------------

In order to interact with the software in the Arduino while it is running, we will create a simple command line interface.

.. literalinclude:: ../../../projects/serial_command_line_interface/src/main.cpp 
    :language: c

Arduino serial port buffer
--------------------------

The Arduio uno has circular RX and TX buffers of 64 bytes. If the buffer is full, incoming data is discarded.


.. code-block:: c
    
    #define SERIAL_TX_BUFFER_SIZE 64
    #define SERIAL_RX_BUFFER_SIZE 64


Connecting two Arduino's using UART
------------------------------------

The following example shows how to communicate between two Ardiuno UNO's using the UART. On each device pin 2 is used for reception (RX), and pin 3 is used for transmission (TX). The TX output of one device is connected to the RX input of the other.

.. figure:: ../../fig/dual_arduino_uart_communication_bb.png
    :scale: 50


.. literalinclude:: ../../../projects/uart/arduino_uart_link/src/main.cpp
    :language: c



Conditionals
==========================
Conditionals are used in order to carry out some actions under some circumstances. In other words, you use conditionals if you want to *do things* in some *conditions* and do some other things for some other conditions.

.. image:: ../../../external/fig/ifElse.png
        :align: center

Let's do a bit of brain storming before implementing the conditionals: You want to turn on an LED if your system stays stays vertical. If your sytem is tilted, then you want to turn off the LED. Can you help me to draw the flow chart?

**Practical example:** Tilt Sensor (if..else)


In some cases, having too many if...else statements one another looks unpleasant. A more compact looking of your code is mode fruitful. Then, *switch..case* concept enters the game.

.. image:: ../../../external/fig/switchCase.png
        :align: center


Let's do a bit more brain storming for switch..case: You want to make a program that asks to the user what s/he wants to do with an LED. If the user presses 1, turn on an LED. If they press 2, turn off LED. Also, in case they forget which number was on/off; if they press 3, there will be a help section. Can you help me to draw the flow chart?

**Practical example:** Switch-case example


.. seealso:: 
	Do you know how a beautiful if...else look like? Check this out :ref:`conditionals_easy`.

..
        Some additions on conditionals
        -------------------------------

        .. todo:: Talk about why choosing if...else wisely is important.

        .. image:: ../../fig/if_else_1.jpeg
                :alt: if...else 1
                :align: center

        .. image:: ../../fig/if_else_2.jpeg
                :alt: if...else 2
                :align: center

        .. image:: ../../fig/if_else_3.jpeg
                :alt: if...else 3
                :align: center

