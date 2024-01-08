:orphan:

.. _stacks_queues_lists:

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


.. code-block:: c

    uint32_t *ptr = (uint32_t *) malloc(10 * sizeof(uint32_t));
    if (ptr == NULL) {
      /* Handle could not allocate memory. */
    } else {

        /* Do something useful with the memory. */
    
        free(ptr);
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



Example with static memory allocation
--------------------------------------

Static memory allocation forces us to restrict the memory usage while writing
the program. This is often safer on systems with small amounts of RAM, as it
reduces the risk of running out of memory (or causing a stack overflow). There
are some important disadvantages, and it is not always a viable option.

One of the disadvantages is that you have to declare enough memory for the
worst case, even though you may only need small amounts at most of the time.
This restricts the amount of memroy that is available for other parts of the
application.


.. literalinclude:: ../../../projects/data_structures/statically_allocated_linked_list_example/src/main.cpp
    :language: c


Circular linked list
--------------------

A circular linked list is a linked list where there is no last element. Instead
of the last element pointing to `NULL` as in the basic linked list, it points
to the first element. In practice this means that the list is traversable from
any point, and the consept of the first element becomes meaningless.



Doubly linked list
------------------

A doubly linked list is a linked list where each element links to both the
next, and the previous element in the list.


Doubly linked list example
---------------------------


.. literalinclude:: ../../../projects/data_structures/doubly_linked_list/src/main.cpp
    :language: c


Stacks
======

A stack is a data structure which follows the last in first out (LIFO) strategy, i.e the last added element to the stack must be the first to leave. Two fundamental operations `push`, and `pop` are supported by the stack.

Stacks are used by many different algorithms, and to implement several useful
features. Typical examples include balancing of symbols, infix to postfix/prefix conversion, and undo commands of editors.

Arduino example
---------------


.. literalinclude:: ../../../projects/data_structures/stack_example_static_allocation/src/main.cpp
    :language: c


Dynamic stack Arduino example
------------------------------

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


.. literalinclude:: ../../../projects/data_structures/queue_example/src/main.cpp
    :language: c



Dynamic queue Arduino example
------------------------------

When enqueueing a new element the following example will allocate the memory dynamically. It is not the recommended approach for systems with limited memory, you should at least add a limit for the maximum memory allocation.

.. literalinclude:: ../../../projects/data_structures/dynamic_queue_example/src/main.cpp
    :language: c


Appendix
========


.. warning:: The data structures beyond this point are not relevant for the exam.


Circular Queue
--------------

A circular queue (also referred to as circular buffer) is a queue where the end of the queue points back to the beginning. The most important reason for doing this is to be able to use the space at the beginning of the queue when the elemens are dequeued. I.e. when you remove a element from the beginning of the queue, there will be a free spot in memory. For a non-circular queue it will not be possible to utilize this memory, as new elements are always added to the end of the queue. In the circular queue when you have reached the end, you will put the next element at the beginning of the queue.

Some implementation with dynamic memory allocation and freeing whill be able to avoid this problem in a different way, but excessive use of the :ccode:`free()` function on the AVR controller will cause memory fragmentation, and it is not a vialbe solution.

In some implementation the oldest data is overwritten if you attempt to add more data when the queue is full. This is often a desirable property, as the recent additions often are more important than the old ones. The queue will typically be of a fixed size anyway, a dynamically resizing queue without any limit is dangerous.

..
  Another benefit is that the queue is of a fixed size, so we will never run out of memroy becauce the queue is filling up.

.. note:: The UART receive buffer on the Arduino is not implemented in this way. If the buffer is full, new data is discarded. This could be problematic, as we are typically more interested in the latest data, than we are in some old, possibly incomplete data. You should at least make sure to poll regulary, in order to avoid the buffer filling up.


.. literalinclude:: ../../../projects/data_structures/circular_queue_example/src/main.cpp
    :language: c


Priority queue
--------------

A priority queue is a queue where each enqueued element has a priority associated with it. When dequeuing, the element with the highest priority is served first. If several elements have the same priority, they are typically served in the same order as they where enqueued. I.e. just like a regular FIFO queue.

Efficient implementations of priority queues are a bit more complex than regular queues, and typically utilize the heap data structure. A simple list approach (such as a linked list) will require us to sort the elements by looping through all the elements, which is much less efficient.



.. literalinclude:: ../../../projects/data_structures/priority_queue_example/src/main.cpp
    :language: c



Event queue
-----------

In embedded systems it is often useful to enqueue external events. Hence a
event queue is a important use case for the queue data structure. A complete
example of such a implementation is a topic for a chapter of it's own.

You may base your event queue on a cicular queue with static memory allocation.

Hashing data structure
======================

A hashing data structure is typically used for large amounts of data elements, when we
require the lookups of a particluar element to be fast.

The data structure typically builds on top of an array, where each element in
the array is accessible through a integer index. A particular key (typically a string) is used for lookup, and a hash function
is used to generate a index based upon the string.


Arduino example
---------------

If the index returned by the hash function is allready in use, we move further
down the table until we find a free position to put our data. This slows down
the operations when the table is starting to fill up.

Some optimization is achieved by considering unused nodes, and deleted nodes to
be different. If we are looking for a particular node (either to read the data,
or to delete it), and the search finds a unused node, we know that it is not there.
If however we find a deleted node we simply continue our search.

.. literalinclude:: ../../../projects/data_structures/hash_table/src/main.cpp
    :language: c


Linked list hash table Arduino example
---------------------------------------

Instead of using the next free position in the table, when the hash function
causes a collision, we may instead us a linked list at each position. If the
has function gives us a index in the table that is in use, we simply add a new
element to the linked list at that location.

.. literalinclude:: ../../../projects/data_structures/hash_table_linked_list/src/main.cpp
    :language: c

Binary tree
===========

.. literalinclude:: ../../../projects/data_structures/binary_tree_example/src/main.cpp
    :language: c


Heap
----


Graph
=====

.. literalinclude:: ../../../projects/data_structures/graph_data_structure_example/src/main.cpp
    :language: c


