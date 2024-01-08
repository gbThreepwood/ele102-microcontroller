:orphan:

.. _L5_edge_conditional_func:

.. note:: *12/02/2020 - 195mins*

    **Aim:**

    - Edge detection ---- Removed to previous lecture
    - Pull-up pull-down resistors: Ex:button connect
    - loops (barely)
    - if..else, Switch case
    - Differences between: Object - Function - Variables
      - nothing such a thing like Method in C++
    - functions


    **Code:**
    - Basic tilt detection
    - Conditionals with tilt detection
    - keypress with switch case
    - blink once/multiple times/and return
    - ? Stopwatch
    - Assignment-1 intro
    

******************************************************************
Conditionals, Functions
******************************************************************
.. ref: https://www.allaboutcircuits.com/projects/learn-how-to-use-the-arduinos-digital-i-o/

The digital inputs and outputs (digital I/O) on the Arduino are what allow you to connect sensors, actuators, and other ICs to the Arduino . Learning how to use the inputs and outputs will allow you to use the Arduino to do some really useful things, such as reading switch inputs, lighting indicators, and controlling relay outputs.


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
   Very nice exercises about conditionals `here. <https://www.w3resource.com/c-programming-exercises/conditional-statement/index.php>`_



Functions
=================

*Functions* are used to organize the actions performed by your sketch into functional blocks. Functions package functionality into well-defined *inputs* (information given to a function) and *outputs* (information provided by a function) that make it easier to structure, maintain, and reuse your code. You are already familiar with the two functions that are in every Arduino sketch: :code:`setup` and :code:`loop`. You create a function by declaring its return type (the information it provides), its name, and any optional parameters (values that the function will receive when it is called). [#f2]_

.. note:: The terms functions and methods are used to refer to well-defined blocks of code that can be called as a single entity by other parts of a program. The C language refers to these as functions. Object-oriented languages such as C++ that expose functionality through classes tend to use the term method. Arduino uses a mix of styles (the example sketches tend to use C-like style, libraries tend to be written to expose C++ class methods). We will use, the term *function* is usually used unless the code is exposed through a class. Don’t worry; if that distinction is not clear to you, treat both terms as the same.

Let's write a simple function that just blinks an LED. It has no parameters and doesn’t return anything (the void preceding the function indicates that nothing will be returned):

**Practical example:** Blink an LED once function

We can also tell the function how many times the LED should blink as an *input parameter*.

**Practical example:** Blink an LED the number of times given in the count parameter

.. seealso:: ANOUNCE ONLY IF ANY EXPERIENCED PROGRAMMER IN THE CLASS!!

   Experienced programmers will note that both functions could be blink because the compiler will differentiate them by the type of values used for the parameter. This behavior is called function overloading. 

A parameter is sometimes referred to as an argument in some documentation. For practical purposes, you can treat these terms as meaning the same thing. I may use them both in various places.

Time to implement *function* concept into our previous blinking code. We can expand the idea a little bit. Let's demonstrate calling a function with a parameter and returning a value. Use the same wiring as the previous exercise. The LED flashes when the program starts and stops when a switch is pressed.

**Practical example:** Blink an LED the number of times given in the count parameter. The program prints the number of times that the LED flashes.

..seealso
  If there is time, ask students to do a tilt detection should return boolean.




.. rubric:: Footnotes

.. [#f1] For wery high power applications a simple current limiting reisitor is not suitable, and you should instead  use power electronics.
.. [#f2] Arduino Cookbook by Michael Margolis Chapter 2.



