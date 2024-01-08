:orphan:

.. _L4_hands_on_:

.. note:: *04/02/2020 - 75mins*

    **Should be finished with Lecture-4**

    - Digital I/O
    - Variable types
    - delay
    - Serial print    

******************************************************************
Various Hands on Activities
******************************************************************
Let's increace the practicality.


Serial Print
==========================

We will be using the following circuit:

.. figure:: ../../fig/single_potmeter_bb.png 
        :align: center
        :scale: 50

.. code-block:: c
  :caption: Analog read from a potentiometer, write the value.

    #define POT_PIN A0

    // the setup routine runs once when you press reset:
    void setup() {
      // initialize serial communication at 9600 bits per second:
      Serial.begin(9600);
    }

    // the loop routine runs over and over again forever:
    void loop() {
      int potVal = analogRead(POT_PIN); // read the input on analog pin
      Serial.println(potVal);  // print out the value you read
      delay(100);        // delay in between reads for stability
    }
