*******************************************************
Additional material for lesson 11
*******************************************************



ANSI Escape sequences
---------------------
If you wish to have control over the behaviour of your serial terminal, you may use Escape sequences.


How to design a reliable communication protocol
-----------------------------------------------

The design of reliable communication protocols is a topic for a complete course on it's own, here I will only provide some general rules to help you avoid the most common pitfalls.


Using the serial port in C#
===========================

Before we go into the details of how to use the serial port, we should first try to understand the basic concept of delegates, and events.

Delegates
--------------------

A delegate in C# is a variable that holds a reference to a method. This is similar to the function pointer concept in C.

Events
-------

A event could be any occurrence of something. The event system in C# is used to notify one part of the software about something that has happened in a different part of the software. This could for instance be a user pressing a button on the keyboard, or mouse, or it could be data received on the serial port.

The event system works by the publisher-subscriber model. A publisher allows one or more subscribers to subscribe to the available events, when a event is raised, a method in the subscriber is called.

The subscribers are added to the publisher by assigning methods to a delegate variable in the publisher.

.. code-block:: c

  using System;
  
  namespace EventsDelegatesTest
  {
      class MainClass
      {
          public static void Main(string[] args)
          {
              var video = new Video() { Title = "Back to the future" };
              VideoEncoder videoEncoder = new VideoEncoder();
              var smsServie = new SMS();
  
              videoEncoder.VideoEncoded += smsServie.OnVideoEncoded;
              videoEncoder.VideoEncodingStarted += smsServie.OnStartEncoding;
  
              videoEncoder.Encode(video);
              Console.WriteLine("Programmet er ferdig.");
          }
      }
  
  }



.. code-block:: c

  using System;
  
  namespace EventsDelegatesTest
  {
      public class VideoEventArgs : EventArgs
      {
          public Video Video { get; set; }
      }
      public class VideoEncoder
      {
          public delegate void VideoEncodedEventHandler(object source, VideoEventArgs args);
          public delegate void VideoEncodingStartedHandler(object source, EventArgs args);
  
          public event VideoEncodedEventHandler VideoEncoded;
          public event VideoEncodingStartedHandler VideoEncodingStarted;
  
          public void Encode(Video video)
          {
              OnVideoEncodingStarted();
  
              Console.WriteLine("Enkodar video..");
  
              OnVideoEncoded(video);
          }
  
          protected virtual void OnVideoEncoded(Video video)
          {
              if (VideoEncoded != null)
              {
                  VideoEncoded(this, new VideoEventArgs() { Video = video });
              }
          }
          protected virtual void OnVideoEncodingStarted()
          {
              if(VideoEncodingStarted != null)
              {
                  VideoEncodingStarted(this, EventArgs.Empty);
              }
          }
      }
  }

.. code-block:: c
  
  using System;
  namespace EventsDelegatesTest
  {
      public class SMS
      {
          public void OnStartEncoding(object source, EventArgs e)
          {
              Console.Write("Kodinga har starta.");
          }
          public void OnVideoEncoded(object source, EventArgs e)
          {
              Console.WriteLine("Sender SMS...");
          }
      }
  }


Consistent Overhead Byte Stuffing (COBS)
-----------------------------------------

.. note:: Byte stuffing is a advanced topic, and will not be covered as part of the course. It is here for the students that are particulary curious.

https://en.wikipedia.org/wiki/Consistent_Overhead_Byte_Stuffing

Debugger
--------

The serial port is often used to print debug messages. When developing an application that uses the serial port for other purposes, it is no longer available for debugging. Arduino UNO contains only one hardware UART, but you can extend your application with software UART's.

The following Arduino program implements a simple bridge between a software UART, and a hardware UART. The software is intended to run on a additional Arduino, to aid in debugging of the code on our primary Arduino. The hardware UART is connected to a PC using USB, while the software UART is connected the the Arduino under test. The allows the Aruino under test to send debug messages using a software UART of it's own.

.. figure:: ../../fig/one_potmeter_and_one_led_with_debugger_bb.png
  :scale: 50

.. literalinclude:: ../../../projects/remote_control/arduino_softserial_debugger/src/main.cpp
  :language: c


Improved example
================

For this improved example we will be using an ASCII based communication protocol. I.e. the data that appear on the UART should be readable when converted to it's character representation in the ASCII code. This introduces some overhead, but has the advantage that the protocol is easy to debug, and it is also testable using a simple UART serial terminal on a PC.

* # - Start flag
* <byte> 
* <byte>
* \n - End flag

Arduino software
-----------------

.. literalinclude:: ../../../projects/remote_control/improved_arduino_slave/src/main.cpp
  :language: c


C# software
-----------