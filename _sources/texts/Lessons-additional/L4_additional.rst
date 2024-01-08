*******************************************************
Additional material for lesson 4
*******************************************************

.. role:: ccode(code)
        :language: c


Functions in C
==============

A function in C is similar to the function you know from mathematics. It is a grouping of statements to perform a task. Every program will have at least one function, which is the first function to be executed when the program starts. As the program grows in size, splitting the code into different functions makes it easier to manage, and helps you to avoid repeating yourself.

Suppose you need to perform the same task twice, e.g. compute the square root of two different numbers. The algorithm will be the same, and thus it makes sense to keep it in a separate function. This will make you code more readable, and it will also save memory. When you are using libraries, you are actually calling functions that have been prepared by others.

There are some important points to note about C functions that makes them different from mathematical functions. A function in C does not have to return a value, the effect of calling the function could be to affect some other part of the system memory without a explicit return value. The result of a function does not have to be the same, even if you provide the same input twice. I.e. the function may depend on some data that is outside it's list of parameters, or it may have memory which is persistent between each call.

Despite these possible differences it is often wise to try and keep as many of the functions as possible behave in a predictable manner. Functions with side effects are harder to test, and debug. Some functions, such as `analogRead()` will depend on the physical state of the analog input, and it would not make sense to have it always behave the same for different calls. A function converting the raw ADC value to a number representing some physical quantity however could be made to always behave the same for a given input. E.g. the input being the raw ADC value, and the output being the numeric value representing the voltage on the pin.


Function syntax
---------------

A function requires a definition, and one or more declarations. The definition is the actual implementation of the function, while the declaration simply informs the compiler that a function with such parameters exist somewhere. The reason declarations are required has to do with how the C language is designed, but a in depth discussion of the reasoning is outside the scope of this lesson.

.. code-block:: c

        /* Declaration */
        uint8_t number_squared(uint8_t number);
        
        /* Definition */
        uint8_t number_squared(uint8_t number){
                return number * number
        }


Functions in Arduino
--------------------

For Arduino we have two functions, :ccode:`setup()`, and :ccode:`loop()` that are always called. In reality there is another function :ccode:`main()` that calls :ccode:`setup()` first, and the it calls :ccode:`loop()` in a infinite loop. The following code is the actual implementation used:

.. code-block:: c
        
        int main(void)
        {
                init();
        
                initVariant();
        
        #if defined(USBCON)
                USBDevice.attach();
        #endif
        
                setup();
        
                for (;;) {
                        loop();
                        if (serialEventRun) serialEventRun();
                }
        
                return 0;
        }


Function to initialize the sytem
--------------------------------

When the controller is booting there is usually some initialization that is required. In order to better organize the code, this could be kept in one or more init functions. The following example illustrates the idea by initializing the UART, as well as one digital input, and one digital output.

.. code-block:: c
        
        void init_controller(){
          Serial.begin(9600);
        
          pinMode(12,INPUT);
          pinMode(11,OUTPUT);
        }

Such organization will make the code more readable, and different initialization functions can be selected depending on the needs in a specific situation.

Function to abstract hardware
------------------------------

In order to make your programs more portable it is advantageous to try and separate as much as possible of the hardware specific code from the actual application. This could be achieved by using functions.

The following is a trivial example of a function which abstracts away the actual digital read for a more general start button function.

.. code-block:: c
        
        uint8_t read_start_button(){
          return digitalRead(3);
        }

It can also be used for digital outputs, such as the following example which simply turns a physical LED on:

.. code-block:: c
        
        uint8_t blue_led_on(){
          return digitalWrite(5, HIGH);
        }


Functions to do calculations
----------------------------

Calculations are good candidates for separation into various functions. The algorithm required to perform a mathematical operation will typically be the same regardless of the actual numbers on which is is employed.

Function to calculate exponentiation
------------------------------------

.. code-block:: c
        
        uint16_t power(uint8_t number, uint8_t exponent){
          uint16_t result = 1;
        
          for(int i = 0; i < exponent; i++){
            result = result*number;
          }
        
          return result;
        }

Function to compute square root
------------------------------------

The following program uses Babylonian method to compute the square root of a number.

.. code-block:: c
        
        float square_root(float number){
        
          float x = number;
          float y = 1;
          float e = 0.00001;
        
          while(x - y > e){
            x = (x + y)/2;
            y = number / x;
          }
          return x;
        }

Returning multiple values
---------------------------------

A function will often need to return more than one value. In this section we will look at some alternative ways to achieve this. 

Using global (external) variables
---------------------------------

Variables that are defined outside any function body are given a global scope. Thus any function will be able to access them. The use of global variables should be limited, but they are useful in some circumstances.

.. code-block:: c
        
        uint8_t resultat_a = 0;
        uint8_t resultat_b = 0;
        int8_t resultat_c = 0
                
        void berekning(uint8_t a, uint8_t b, uint8_t c){
        
          resultat_a = a + b + c;
          resultat_b = a*b*c;
          resultat_c = (a - b)*c;
        
          return;
        }


Using a data structure
----------------------

The :ccode:`return` statement makes the function return to the caller, providing a single populated data type. The data type could be a integer, or it could be some more advanced data type. If one needs to return more than one value, a custom data type could be used.

.. code-block:: c
        
        typedef struct {
          uint8_t resultat_a;
          uint8_t resultat_b;
          int8_t resultat_c;
        } tall_t;
                
        tall_t berekning2(uint8_t a, uint8_t b, uint8_t c){
        
          tall_t result;
        
          result.resultat_a = a + b + c;
          result.resultat_b = a*b*c;
          result.resultat_c = (a - b)*c;
        
          return result;
        }

Using pointers
--------------

Instead of returning the data, the function could manipulate the memory address of data in the calling function. This is achieved using pointers, i.e. variables containing memory addresses to other variables. Pointers are discussed in the section :ref:`using-pointers-in-c`

.. code-block:: c
        
        void berekning3(uint8_t a, uint8_t b, uint8_t c, uint8_t *resultat_a, uint8_t *resultat_b, int8_t *resultat_c){
        
          *resultat_a = a + b + c;
          *resultat_b = a*b*c;
          *resultat_c = (a - b)*c;
        }

.. _using-pointers-in-c:

Using pointers in C
===================

Pointers have many useful applications besides passing data to and from functions.

The following example shows how one can assign specific memory addresses to pointers, and then use the pointers to manipulate those memory locations. The example blinks a LED connected to pin 5 on the Arduino UNO.

.. code-block:: c
        
        /*
         * This function demonstrates direct register access for Atmega 328.
         * You should never use this function for anything but a fun demonstration.
         * Use the official register map files for any production development.
         *
         * PORTD is digital pin 0 - 7 on the Arduino UNO.
         */
        void register_access_led_blink(){
        
          uint8_t * port_d_register = (uint8_t*)0x0b;
          uint8_t * data_direction_register_d = (uint8_t*)0x0a;
          uint8_t * pin_d_register = (uint8_t*)0x09;
        
          uint8_t * port_d_register_full_address = port_d_register + 0x20;
          uint8_t * data_direction_register_d_full_address = data_direction_register_d + 0x20;
          uint8_t * pin_d_register_full_address = pin_d_register + 0x20;
        
          *data_direction_register_d_full_address |= 0b11111100;
        
          for(;;){
            *port_d_register_full_address ^= (1 << 5); // Toggle output pin 5.
            delay(100);
          }
        }
        

Void pointers
-------------

When declaring a pointer we normally declare the data type, e.g. :ccode:`uint8_t *pointer = &variablename;`. Any address in the Atmega 328 is a 16-bit number (the actual address bus might not be 16-bit, but it is more than 8-bit and thus 16-bits are used).

A void pointer :ccode:`void*` is a pointer that has no data type associated with it. It still takes up the same amount of memory (16-bit on Atmega 328), but it can be used to point to any data type.

Function pointers
-----------------

Just like pointers to data, the C language also supports pointers to functions. This is a powerful (but advanced) feature of the language, which allows us to implement many usefull design patterns more efficiently.

.. code-block:: c
        
        void function(int a)
        {
            Serial.print(a);
        }
        
        int main()
        {
            void (*fun_ptr)(int) = &function;
        
            (*fun_ptr)(22);
        
            return 0;
        }


Recursive calling of functions
==============================

If a function is invoking another instace of itself, this is called a recursive function call. This is usefull in many algorithms.

Example 1 - Factorial function
------------------------------

.. math::
    n! = n(n - 1)*(n - 2)*(n - 3) \dots 

.. code-block:: c
    
    uint32_t factorial(uint8_t number){
      if(number <= 1){
        return 1;
      }
    
      return number * factorial(number - 1);
    }



Example 2 - Fibonacci Series
-----------------------------

.. todo:: Implement example


Functions in separate files
============================

It is often useful to group related functions in a library by putting them in dedicated files. This is usually achieved by placing the definitions in a source file (\*.c, or \*.cpp), while the declarations are placed in a header (\*.h) file.



Using the tilt sensor
=====================

According to the datasheet, the sensor will disconnect all four pins when the switch is tilted more than 45 degree. Considering how the switch is constructed this seems unlikely, and it would appear that it is possible to detect multiple orientations.

The following source code illustrates how to detect all 6 detectable orientations. Note that this is an advanced example, and also that it is not too reliable because of how the switch operates.

.. literalinclude:: ../../../projects/platformio/digital-input/tilt-switch/src/main.cpp
    :language: c
    :linenos:

