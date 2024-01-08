:orphan:

.. _State_macines:

************************************************************
State machines and how to implement them on microcontrollers
************************************************************

In order to systematically design and implement the intended behaviour of a embedded system, it is often useful to think in terms of state machines. This will help us to break down complex problems into simpler parts. Another benefit is that it often simplifies the design of software that solves multiple tasks simultaneously.


Mealy and Moore machines
========================

State machines can be divided into two major categories Mealy and Moore, named after their inventors. In a Mealy state machine the output is dependend on the current state, as well as the inputs. In a Moore state machine the output depends only on the current state.


Hierarchical state machine
==========================
A common issue with traditional state machines is that similar (but not exactly equal) states causes the need for repeating the same behaviour in many states. This is known as the "state-explosion” phenomenon.

In order to overcome this issue the hierarchical state machines has been introduced as a way of capturing the common behaviour of different states. In a hierarchical state machine, a given state may exist as a substate of a more generic superstate. Any event not handled by the substate will automatically be passed on to the superstate. Thus the substate only implements the difference from the superstate. This is known as behavioural inheritance.

Visual representation of state machines using UML
=================================================



State machine implementation in C
=================================

.. warning::
        Rewrite this code to Arduino specific examples, and use external LED's, switches and similar to visualize the operation.

Simple switch case based implementation
---------------------------------------

.. code-block:: c

        /********************************************************************/
        #include <stdio.h>
        
        /********************************************************************/
        typedef enum {
                ST_RADIO,
                ST_CD
        } STATES;
        
        typedef enum {
                EVT_MODE,
                EVT_NEXT
        } EVENTS;
        
        EVENTS readEventFromMessageQueue(void);
        
        /********************************************************************/
        int main(void)
        {
          /* Default state is radio */
          STATES state = ST_RADIO;
          int stationNumber = 0;
          int trackNumber = 0;
        
          /* Infinite loop */
          while(1)
          {
            /* Read the next incoming event. Usually this is a blocking function. */
            EVENTS event = readEventFromMessageQueue();
        
            /* Switch the state and the event to execute the right transition. */
            switch(state)
            {
              case ST_RADIO:
                switch(event)
                {
                  case EVT_MODE:
                    /* Change the state */
                    state = ST_CD;
                    break;
                  case EVT_NEXT:
                    /* Increase the station number */
                    stationNumber++;
                    break;
                }
                break;
        
              case ST_CD:
                switch(event)
                {
                  case EVT_MODE:
                    /* Change the state */
                    state = ST_RADIO;
                    break;
                  case EVT_NEXT:
                    /* Go to the next track */
                    trackNumber++;
                    break;
                }
                break;
            }
          }
        }


Function pointer based implementation
-------------------------------------

.. code-block:: c

        #include <stdio.h>

        struct state;
        typedef void state_fn(struct state *);

        struct state
        {
            state_fn * next;
            int i; // data
        };
        
        state_fn foo, bar;
        
        void foo(struct state * state)
        {
            printf("%s %i\n", __func__, ++state->i);
            state->next = bar;
        }
        
        void bar(struct state * state)
        {
            printf("%s %i\n", __func__, ++state->i);
            state->next = state->i < 10 ? foo : 0;
        }
        
        int main(void)
        {
            struct state state = { foo, 0 };
            while(state.next) state.next(&state);
        }



Example: Traffic light control
------------------------------

A traffic light control system is a classical example of a problem that could be solved using a state machine.


Example: Elevator controller
----------------------------



State machine library
=====================

http://www.state-machine.com/
