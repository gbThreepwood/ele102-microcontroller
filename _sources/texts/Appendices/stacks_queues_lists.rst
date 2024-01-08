.. stacks_queues_lists:

************************************
Stacks, queues and lists
************************************

.. role:: ccode(code)
        :language: c


.. warning:: In embedded systems with limited amounts of RAM you should typically avoid dynamic memory allocation. I.e. avoid the malloc() function. As an alternative to dynamic allocation, static allocation (i.e. compile time allocation) is possible. E.g by putting your data structures in an statically allocated array. Still for completeness several of the examples in this chapter uses dynamic memory allocation. High end microcontrollers, and especially microcontrollers running a operating system to manage memory could safely use this approach.

Memory allocation
=================

Memory allocation is the process of reserving some part of the memory for a particluar purpose. We distinguish between static and dynamic allocation, where the former is compile time allocation, while the latter is run time allocation. Finally there is also automatic allocation, which is the allocation of memory for the variables when you enter a new functions, or more generally when you enter a new scope.

Static memory allocation
------------------------

Variables with static allocation include global variables, file scope variables, and variables inside functions that are declared with the :ccode:`static` modifier. The memory for these variables is reserved as long as your program is running, and it may not be resized. 

Dynamic memory allocation
--------------------------

In the C standard we have four functions for dynamic memory allocation:

* :ccode:`malloc()`
* :ccode:`calloc()`
* :ccode:`realloc()`
* :ccode:`free()`

C++ offers the :ccode:`new`, and :ccode:`delete` operators as a alternative to the C functions, but the C functions are also supported in C++.

The actual implementation of the memory allocation functions vary depending on the arcitecture, and operating system in use. On the AVR microcontroller a rather simple function is used: https://www.nongnu.org/avr-libc/user-manual/malloc.html

The following code illustrates how to dynamically allocate memory for 10 unsigned 32 bit integers. I.e. it allocates 320 bit, or 40 bytes.

.. code-block:: c

    uint32_t *ptr = (uint32_t *) malloc(10 * sizeof(uint32_t));
    if (NULL == ptr) {
      /* Handle could not allocate memory. */
    } else {

        /* Do something useful with the memory. */
    
        free(ptr); /* Free the memory (on a MCU this will often cause memory fragmentation) */
        ptr = NULL; 
    }

Custom memory allocation function
----------------------------------

On microcontrollers with small amounts of RAM, dynamic memory allocation could be dangerous. As an alternative one may declare a static array, and write a special function to declare parts of the array for specific purposes. A lock on the function provides additional safety, it may be used to prevent allocation of more memory after the microcontroller has finished it's initialization procedure.

.. literalinclude:: ../../../projects/data_structures/static_memory_allocation/src/main.cpp
  :language: c


Linked List Data Structure
===========================

A linked list is a data structure where each element links to the next element
using a pointer.

Manual assignment of elements in linked list
----------------------------------------------

The following example shows a static implementation of a linked list, where the
links are made by explicitly assigning addresses to the next pointers. This is
not how you would do it in practice, it only serves to demonstrate the simplest
possible list you could make.

.. literalinclude:: ../../../projects/data_structures/linked_list_example/src/main.cpp
    :language: c


Linked list demo with various  useful functions
------------------------------------------------


.. literalinclude:: ../../../projects/data_structures/linked_list_example_2/src/main.cpp
    :language: c


Stacks
======

A stack is a data structure which follows the last in first out (LIFO) strategy, i.e the last added element to the stack must be the first to leave. Two fundamental operations `push`, and `pop` are supported by the stack. The `push` operation adds a new element on to the stack, while the `pop` operation removes a element, returning the element to the user. In addidion a third operation `peek` is often supported, in order to look at the top element in the stack without removing it.

Stacks are used by many different algorithms. Typical examples include balancing of symbols, infix to postfix/prefix conversion, and undo commands of editors.

Example with statically allocated memory
-----------------------------------------


.. literalinclude:: ../../../projects/data_structures/stack_example_static_allocation/src/main.cpp
    :language: c


Dynamic memory allocation stack example
-----------------------------------------

.. literalinclude:: ../../../projects/data_structures/dynamic_stack_example/src/main.cpp
    :language: c

Queues
======

A queue is a data structure which follows the first in first out (FIFO)
strategy. The fundamental operations are `enqueue`, and `dequeue`.

Queues are used for enqueuing of operations, or events. E.g. if several events
happen before the controller is able to process them, they should be processed
in the same order that they occured.

One example is the receive and transmit buffers used by the Serial library for
UART communication in the Arduino. E.g. for reception, a RX complete interrupt is used to place the
next received byte in the queue, while :ccode:`Serial.read()` retrives the oldest byte from the queue.

Static queue Arduino example
-----------------------------

The following example uses a statically allocated array for the queue. The array will be filled at the tail, and data will be removed at the head. This eventually uses up all the allocated memory, as the free memory at the front of the queue is unavailable. There are many ways to address this problem, but for simplicity this first example ignores the problem.

.. literalinclude:: ../../../projects/data_structures/queue_example_static_allocation/src/main.cpp
    :language: c



Dynamic queue Arduino example
------------------------------

When enqueueing a new element the following example will allocate the memory dynamically. It is not the recommended approach for systems with limited memory, you should at least add a limit for the maximum memory allocation.

.. literalinclude:: ../../../projects/data_structures/dynamic_queue_example/src/main.cpp
    :language: c


