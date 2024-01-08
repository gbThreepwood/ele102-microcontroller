*******************************************************
Additional material for lesson 12
*******************************************************

I2C
===
SPI in Arduino
================

The official Arduino library for SPI communication is simply know as SPI, this library only supports operating in master mode. More information is available in the `spi documentation <https://www.arduino.cc/en/reference/SPI>`_. In order to implement an Arduino SPI slave we will be using direct register manipluation.


.. figure:: ../../fig/dual_arduino_spi_communication_bb.png
    :scale: 50


.. warning:: This code is not ready

Master
~~~~~~

.. literalinclude:: ../../../projects/spi/simple_spi_master/src/main.cpp
    :language: c


Slave
~~~~~~

.. warning:: Direct register manipluation is outside the curriculum of the course, thus you do not have to fully understand this code. You may simply use it on one Arduino device, in order to test the master code on another Arduino device.


.. literalinclude:: ../../../projects/spi/simple_spi_slave/src/main.cpp
    :language: c

SPI I/O port expander
-----------------------

A I/O port expander is a device that provides more digital I/O pins for the microcontroller, accessible using bus communication. Often this will be a specially made integrated circuit, such as the MCP23S17. It is possible to emulate the features of this chip using software on the Arduino.

In this example we will be using SPI to control the LED's, and read the push buttons on one Arduino from another.


Master
~~~~~~

.. literalinclude:: ../../../projects/spi/spi_io_port_expander_master/src/main.cpp
    :language: c


Slave
~~~~~~

.. warning:: Direct register manipluation is outside the curriculum of the course, thus you do not have to fully understand this code. You may simply use it on one Arduino device, and consider the Arduino running this code to be a simulation of a I/O expansion chip.


.. literalinclude:: ../../../projects/spi/spi_io_port_expander_slave/src/main.cpp
    :language: c


General suggested reading:
https://ele102.gitlab.io/automatisering-frde/ele102-frde/texts/Lessons/L9_communication.html

General suggested reading: Canvas > Modules > Oreilly Arduino Cookbook
2nd Edition > Chapter 13 - Communicating Using I2C and SPI

A summary of everything video: https://youtu.be/IyGwvGzrqp8

UART (backup)
==============

Suggested reading:
https://www.circuitbasics.com/how-to-set-up-uart-communication-for-arduino/

..
  .. figure:: /home/gizem/snap/typora/33/.config/Typora/typora-user-images/image-20210325100740492.png
     :alt: image-20210325100740492

     image-20210325100740492

**Sender:**

.. code:: c

   #include <SoftwareSerial.h>
    
   SoftwareSerial softSerial(2,3);  // RX, TX 
   void setup()  {
     Serial.begin(9600); 
     softSerial.begin(9600);
   } 
   void loop()  { 
   // Check for received characters from the computer 
     if (Serial.available())  { 
   // Write what is received to the soft serial 
       char x = Serial.read();
       softSerial.write(x); 
       Serial.print("Sending:");
       Serial.println(x);
     }
   }

**Receiver:**

.. code:: c

   #include <SoftwareSerial.h>

   SoftwareSerial softSerial(2,3);

   void setup()  { 
   softSerial.begin(9600); 
   Serial.begin(9600);
   // pinMode(LED, OUTPUT);
   } 
   void loop()  { 
   // When our comm channel is active: 
     if (softSerial.available())  { 

       char data_received = softSerial.read(); 
       Serial.print("Received:");
       Serial.println(data_received);
       //Connect an LED later
     } 
   }


Suggested reading: https://microdigisoft.com/communicate-using-spi-between-two-arduino-boards/

I2C (backup)
=============

Suggested reading:
https://howtomechatronics.com/tutorials/arduino/how-i2c-communication-works-and-how-to-use-it-with-arduino/

https://www.circuitbasics.com/how-to-set-up-i2c-communication-for-arduino/

https://dronebotworkshop.com/i2c-arduino-arduino/

.. 
  .. figure:: /home/gizem/snap/typora/33/.config/Typora/typora-user-images/image-20210325134005680.png
     :alt: image-20210325134005680

     image-20210325134005680

**Master**

.. code:: c

   #include <Wire.h>

   const uint8_t slave_address = 1;

   void setup() {
     Wire.begin();  // Start the I2C protocol as master
     
     Serial.begin(9600); 
     Serial.println("I2C Master");
   }

   byte data_sent = 0;

   void loop() {
     Wire.beginTransmission(slave_address);                
     Wire.write("data: ");       
     Wire.write(data_sent);             
     Wire.endTransmission();    
        
     delay(500);
     
     Wire.requestFrom(slave_address, 4); //request 4 byte from this slave
     while (Wire.available()) {
       char ch = Wire.read(); 
       Serial.print(ch);        
     }    
     data_sent++;
     if(data_sent==6)data_sent=0;  
   }

**Slave:**

.. code:: c

   #include <Wire.h>

   const uint8_t slave_address = 1;

   void setup() {
     Wire.begin(slave_address);                
     Wire.onRequest(requestEvent); 
     Wire.onReceive(receiveEvent); 
     
     Serial.begin(9600);   
     Serial.println("I2C Slave");
   }

   void loop() {
     delay(500); // just do nothing
   }

   void receiveEvent(int data_received) { 
     // to be called when a slave device receives a transmission from a master.
     while (Wire.available()>1) { 
       char ch = Wire.read(); 
       Serial.print(ch);         
     }
     int x = Wire.read();    
     Serial.println(x);      
   }

   void requestEvent() { 
     // to be called when a master requests data from this slave device
     // (on-demand function)
     // better to have, not essential
     Wire.write("gzm\n");   
   }

**Slave with LED**

.. code:: c

   #include <Wire.h>

   const uint8_t slave_address = 1;
   const uint8_t led_pin = 13;

   int val = 0;

   void setup() {
     Wire.begin(slave_address);                
     Wire.onRequest(requestEvent); 
     Wire.onReceive(receiveEvent); 
     
     Serial.begin(9600);   
     Serial.println("I2C Slave");
   }

   void loop() {
     delay(500); // just do nothing
   }

   void receiveEvent(int data_received) { 
     // to be called when a slave device receives a transmission from a master.
     while (Wire.available()>1) { 
       char ch = Wire.read(); // read the first group "data:"
       Serial.print(ch);         
     }
     int x = Wire.read(); // read the second group "number"    
     Serial.println(x);
     if(x == 0){
       Serial.println("LED Toggle!");
       Serial.println(val);
       val = !val;
       digitalWrite(led_pin, val);
     }
   }

   void requestEvent() { 
     // to be called when a master requests data from this slave device
     // (on-demand function)
     // better to have, not essential
     Wire.write("gzm\n");   
   }

Suggested reading:
https://www.circuitbasics.com/how-to-set-up-spi-communication-for-arduino/
https://www.circuitbasics.com/how-to-set-up-spi-communication-for-arduino/

