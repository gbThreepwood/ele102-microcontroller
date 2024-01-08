*******************************************************
Additional material for lesson 9
*******************************************************


.. _L9_memory_pointer:

Changing the variable through its adress: Pointers
====================================================

Create a pointer to memory address 0x002b:

.. code-block:: c

  uint8_t *port_d = (uint8_t*)0x002b;

Write a 1 to bit number 4 (note the asterisk :code:`*` in front of the variable name):

.. code-block:: c

    *port_d = 0b00001000;

Without the asterisk we are changing the contents of the variable, rather than the memory location to which it points:

.. code-block:: c

  port_d = 0b00001000; // Attempts to set the variable to point to memory address 0x0008. Don't do this!

The following example illustrates how to blink a LED connected to pin 3 on the Arduino UNO:

.. literalinclude:: ../../../projects/platformio/architecture/register-address-specification-led-blink/src/main.cpp
    :language: c
    :linenos:

Note that the register accesses modify all the bits in the register. E.g. if pin 5 was high before, it will be written low even though we are only interested in modifying pin 3. This can be solved by first reading the register and using some logic operations before writing back to the register.

Exercise: blink a LED connected to pin 8
-----------------------------------------

#. Use the Arduino UNO pinout diagram to figure out which port (bank of I/O pins) that pin 8 is connected to.
#. Use the datasheet of the Atmega328p (page 280) to figure out the memory addresses of the registers which controls the bank of pins of which pin 8 is a member. Use the addresses in parentheses.
#. Write a short explanation in your own words about the difference between the addresses in parentheses and the ones without.
#. Modify the program in the previous example to blink a LED connected to pin 8.


Reading and manipulating single bits
------------------------------------

In order to manipulate single bits of a register we have to read the register, make changes using logic operations, and finally write back the result.

A very useful operator for this purpose is the left bit shift (:code:`<<`) operator. It allows us to shift a binary value to the left (a right shift operator :code:`>>` is also available).

.. code-block:: c

  uint8_t b = (1 << 3); // The value of 'b' is 1 shifted 3 places to the left, i.e. 0b1000, or 8 in decimal.

Additionally the bitwise and (:code:`&`), the bitwise or (:code:`|`), the bitwise xor (:code:`^`), and the bitwise not (:code:`~`) can be used in clever ways to set, clear, or flip the state of single bits while leaving the other bits untouched.

.. literalinclude:: ../../../projects/platformio/architecture/register-access-single-bit-manipulations/src/main.cpp
    :language: c
    :linenos:


Exercise: analyze the above example
-------------------------------------------

#. Verify the operation of the above code listing by testing it on a Arduino UNO. Connect a push button with pull-down resistor to pin 4, and a LED to pin 3.
#. Use your knowledge about digital electronics to analyze the above code listing. Especially the lines which read or manipulate the registers. Consider the case that PORD = 0b11000000, PIND = 0, and DDRD = 0, before the program starts. Write down your explanation for each statement.


Exercise: read a digital input on pin A5
-------------------------------------------

The analog input pins can be reconfigured to work as digital input or output pins. In this exercise you are going to use the pin A5 as a digital input by means of direct manipulation of the memory registers.

#. Use the pinout diagram, and the datasheet of the Atmega328p to figure out the memory addresses of the registers which controls the bank of pins of which pin A5 is a member.
#. Configure pin A5 as an input.
#. Read the state of the A5 pin, and blink a LED if A5 is high. If A5 is low the blinking should stop and the LED should be low. You may use the normal :code:`digitalWrite()` function for the LED control.


Determine RAM memory usage
==========================

In many applications it is not possible to determine the RAM usage during compile time. It might depend on the amount of external input that the micocontroller receives.

The compiler defines some symbols that may be used to compute the RAM usage at run time. 

`__heap_start`
`__brkval`

.. code-block:: c

        extern unsigned int __bss_end;
        extern unsigned int __heap_start;
        extern void *__brkval;
        int freeMemory() {
          int free_memory;
        
          if((int)__brkval == 0)
            free_memory = ((int)&free_memory) - ((int)&__bss_end);
          else
            free_memory = ((int)&free_memory) - ((int)__brkval);
        
          return free_memory;
        }


.. _ex_stack_usage:

Exercise: determine stack usage
===============================

There is a macro :code:`RAMEND` somewhere in the libraries that are included as part of your Atmega328p project. This macro is defined as the hexadecimal value of the highest address in the internal RAM (which according to the memory map in the datasheet of the Atmega328p page 18, is 0x8FF). By allocating a new variable, and obtaining the address of this variable by means of the :code:`&` operator, it is possible to calculate the number of bytes on the stack.

.. code-block:: c

  uint8_t variable_at_top_of_stack = 1;
  uint16_t stack_size = RAMEND - (uint16_t)&variable_at_top_of_stack + 1;
  //uint16_t stack_size = RAMEND - SP;

#. Use the above code lines in a program to determine the number of bytes on the stack.
#. Print the value to the UART.
#. Add some new variables to the stack, and observe how the stack size value changes. You add variables to the stack simply by declaring them inside a function. Due to compiler optimization you have to either use the variables for something, or declare them as :code:`volatile`. Otherwise the compiler will optimize them away, and your stack size will not change.
#. The compiler may choose to organize the data in the stack in another order than it is declared in the code. Thus it might not always yield correct results to use the address of a variable. The third (commented) line in the above example uses the macor :code:`SP` (stack pointer) to obtain the address to the top of the stack. This is probably a better method, but both are provided for 


Using the EEPROM
================


Manage the memory stucture for complex projects
-----------------------------------------------


NAND Flash
----------

NOR Flash
---------


Using external memory
=====================

External EEPROM
---------------

SD-card
-------

Special memory types
=====================

NVRAM

.. .. youtube:: https://www.youtube.com/watch?v=VzG4MmGOTOQ

NVSRAM

NVSRAM is a special type of hybrid non-volatile random-access memory (NVRAM). One portion is a normal volatile SRAM, while the other is some form of EEPROM which has lower read and write speeds, but allows storing the memory contents in a non volatile manner.

EERAM is similar in concept to NVSRAM. Whenever it detects a power failure it will transfer the contents of the SRAM to a non volatile storage.

OTP EPROM

Historical memory types
------------------------

In this section we will briefly discuss some of the memory technologies that are not used anymore.





Random access memory (RAM)
---------------------------


