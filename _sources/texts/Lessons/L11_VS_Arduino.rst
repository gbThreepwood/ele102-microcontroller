*****************************************
PC (C#) and Arduino Communication
*****************************************

In this lesson we will go through the design of a application that allows us to communicate between a desktop application written in C#, and a Arduino application written in C/C++. The communication link will be using the UART, but with some additional higher level application specific protocol layers.

Designing a simple communication protocol on top of UART
=========================================================

Among the low level methods that are available to us are `Serial.write()`, and `Serial.read()`. The methods have the ability to send and receive one byte respectively. They do hovever not put any constraints on what the bytes are supposed to represent. The binary value 0b01001011 could represent a uppercase "K", as it does in ASCII, but it could also be a command to turn on a LED, or read a analog input. It is up to the designer to decide.

Even though you are free to do as you please, there are some approaches that are considered good practice, and that often lead to a more reliable communication system. For all but the most simplistic systems we need to send more data than there is room for in a single byte. Thus we typically pack multiple bytes together in what is known as a data packet.

The following listing is one possible organization of a data packet: 

* Start flag (single constant byte signifying start of transmission)
* Command byte or packet type (one or more bytes that informs the receiver on what the data represents)
* Length byte (the number of data bytes, or possibly the total number of bytes.)
* Data byte(s) (the actual data to transfer)
* Checksum or CRC (data used to verify the integrity of the transmitted data)
* End flag (single constant byte signifying end of transmission, possibly the same value as the start flag)

For fixed length data packages the length byte is not needed. In simple applications the checksum is often omitted, and possibly also the start and end flags.

For advanced applications there is a possible issue that the start, and end flags could appear as part of the data bytes. If each byte of the data could be anything, that means it could also be the flags no matter which value you choose to use. The flags are introduced to increase reliability, and this benefit is impaired if you do not reset the reception each time you receive a start flag. Thus the byte sequence of the start flag must never be transmitted except at the beginning (and possibly at the end) of a packet. In order to overcome this issue one may use various forms of byte stuffing to encode data which happen to use the same byte sequence as the start flag.

For production code it is recommended to at least use the following organization of the data even for simple applications:

* Start flag (single constant byte signifying start of transmission)
* Command byte (one or more bytes that informs the receiver on what the data represents)
* Data byte (the actual data to transfer)
* End flag (single constant byte signifying end of transmission)

In the following examples we will try to start of even simpler however.



Using the serial port in C#
============================

.. code-block:: csharp

  port = new SerialPort("COM19", 9600, Parity.None, 8, StopBits.One);
  port.Open();
  port.Write("led");
  port.Close();


Using the serial port in C# (System.IO.Ports)
---------------------------------------------

When reading the serial port you have several options. You could use a blocking call that blocks until data is available on the serial port, or you could poll the serial port library to check if data is available. Finally you could register a event handler that is called when data is available.


Simple example
==============

In this example we wil create a application to control an LED, and read a analog value from a potmeter connected to our Arduino.

.. figure:: ../../fig/one_potmeter_and_one_led_bb.png
  :scale: 50


Arduino software
----------------

Ideally you should not have to concern yourself with what kind of programming language will be used on one side of the communication link, when developing software on the other side. In our case that means that you should not need to know C# in order to develop the Arduino software, and you should not have to know C or Arduino in order to develop the C# software.

For this to work you should write a protocol specification before you start developing the software. In this first approach the following protocol is implemented (please note that this is a very naive approach):

Command: 'led' causes a LED on pin 9 to toggle. Command 'voltage' causes the Arduino to return the measured raw analog value on pin A0. No additional control data is transmitted, the controller does not return a confirmation message when it is told to toggle the LED, and the reception relies on a timeout insted of a end flag to signify complete data packet.

One could also note that the data is transmitted as ASCII, which means that a number that ideally takes up one byte, will use two bytes. And also the length of the commands are many bytes, which introduces unnecessary overhead.


.. literalinclude:: ../../../projects/remote_control/simple_arduino_slave/src/main.cpp
  :language: c


C# software
-----------

The following C# code listing provides an example of on how to transmit a character over the UART. It will however need to be adapted if you intend to use it in the following exercise.

.. code-block:: csharp
    
  using System;
  using System.Collections.Generic;
  using System.ComponentModel;
  using System.Data;
  using System.Drawing;
  using System.IO.Ports;
  using System.Linq;
  using System.Text;
  using System.Threading.Tasks;
  using System.Windows.Forms;
  namespace ArduinoControl
  {
      public partial class Form1 : Form
      {
          public Form1()
          {
              InitializeComponent();
          }
          private void onButton_Click(object sender, EventArgs e)
          {
              try
              {
                  SerialPort sp = new SerialPort("COM6", 9600);
                  sp.Open();
                  sp.Write("H");
                  sp.Close();
              }
              catch (Exception ex)
              {
                  MessageBox.Show(ex.Message);
              }
          }
          private void offButton_Click(object sender, EventArgs e)
          {
              try
              {
                  SerialPort sp = new SerialPort("COM1", 9600);
                  sp.Open();
                  sp.Write("L");
                  sp.Close();
              }
              catch (Exception ex)
              {
                  MessageBox.Show(ex.Message);
              }
          }
      }
  }


Exercise: Toggle LED from GUI program on PC
-------------------------------------------

In this exercise you will be writing a program to toggle a LED, and to read a value from a potentiometer connected to the Arduino UNO. The GUI application should look similar to the following screenshot:

.. figure:: ../../fig/software/arduino_comm_screenshot.png
  :scale: 100

#. Start by connecting a LED, and a potentiometer to the Arduino UNO.
#. Create a program on the Arduino which toggles the LED when it receive the string "led", and who transmits the raw ADC value when it receive the string "voltage".
#. Test the Arduino program in the serial monitor. By quickly typing the command strings on your keyboard, you should be able to verify all aspects of the operation.
#. Create a GUI application in Visual Studio (not Visual studio code), with a similar appearance to the above screenshot.
#. Add the functionality to transmit data to the UART upon pressing the push buttons. Verify that you are able to toggle the LED state.
#. Add the functionality to read the potentiometer ADC value.

Advanced example
================

When you want to exchange various data in one transaction than you need to start a "data package". In this example, we will create our custom data package, create our own protocol on top of UART.

.. figure:: ../../fig/one_potmeter_and_three_led_bb.png
  :scale: 50


Arduino software
----------------

.. Let's write together.


C# software
-----------

.. figure:: ../../fig/software/adv_arduino_comm_screenshot.png
  :scale: 100 

