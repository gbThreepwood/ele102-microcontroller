*******************************************************
Additional material for lesson 2
*******************************************************



Exercise: D-flip flop, and state machines (voluntary)
------------------------------------------------------

The ideal way to implement state machines in software differs from the hardware approach. The concepts covered so far however, limits our possibilities, and in this exercise you will implement a state machine using the same techniques as you know from digital electronics. In digital electronics we use flip-flops to store data, but in our microcontroller we can easily store data by declaring a variable for it. Thus the implementation of flip-flops in this way is rather silly, but still a useful learning experience.

#. Implement a D latch
#. Implement a D flip-flop using two D latches
#. Use two D flip-flops to implement a state machine with 4 states. Usa a push button to provide the clock signal, and 4 LEDs to decode the current state.

.. todo:: Add a drawing of the state diagram that we are going to use. A regular 3-bit counter which counts from 0 to 7, and then resets back to 0.

.. warning:: Turns out the code becomes rather complex. In order to prevent a mess, it was also necessary to use some advanced programming techniques to structure the code. You should note however that it would be possible to make it work using only the theory covered so far.

The following code listing provides a solution proposal, where the D flip-flop is defined in a function to allow easy re-use of the code. If you do not know how to use functions, you will have to duplicate some of the code in order to make two flip-flops.

.. literalinclude:: ../../../projects/platformio/d-flip-flop-state-machine/src/main.cpp
   :language: c


.. todo:: Add solution proposal using common software techniques, in order to demonstrate the proper way to solve such problems in software.