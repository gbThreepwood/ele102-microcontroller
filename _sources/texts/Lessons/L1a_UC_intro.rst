.. _L1_UC_intro:            

..
   .. note:: *20/01/2021*

       **Aim:**

       FIRST PART

         - Microcontroller concept very simply.
         - Embedded systems
         - Where to use them
         - How should your system work? reliability: every time all the time!
         - Differences between IC/MCU/microprocessor/embedded system

       SECOND PART

         - what is a compiler and how does it work
         - memory in microcontrollers
         - first pass, 2nd pass in program
         - how to relate 1010's with printf(), where is assembly in here
         - Simple Computer with assembly
         - so we decided on Arduino


       **Materials:**

       None

       **Code:**

       None



***************************
What is a Microcontroller
***************************

We will start this course by exploring the questions of what a microcontroller is, and what it means to be developing software for a embedded system.

Simply put a microcontroller is a small computer on a single integrated circuit (a chip), containing a processor core, memory, and programmable input/output peripherals [#f1]_.

- Tiny, self­contained computers in an IC
- Requires few external components to maintain the core functionality
- Often contain additional peripherals
- Different packages available
- Vast array of performance categories available

A microcontroller alone is generally not a finished product. Microcontrollers are used at the core of so called *embedded systems* to facilitate control of the system. A embedded system can be used in almost unlimited number of applications, from your microwave oven, to the *Perseverance Rover* on Mars.

Not only is a microcontroller alone pretty useless, it is also typically not able to operate unless it has at least a few external components. It is not that it would be impossible to incorporate everything inside the chip, but this would lead to a dramatic reduction in the flexibility of the controller to be adapted to different applications. Of the most prominent examples of required external circuitry a regulated power supply should be mentioned.

..
   Microcontrollers are generally not the products. The systems/products developes using microcontrollers are called as a product of *Embedded Systems*. Terms are generally confused!

A quick google search for the keyword *microcontroller* reveals some of the many integrated circuits, and some common experimentation board for microcontrollers:

.. figure:: ../../../external/fig/microcontrollerFail.png
          :alt: Google search for microcontroller
          :align: center


Embedded Systems
--------------------
**Definition 1:** Information processing systems embedded into enclosing products such as cars, telecommunication or fabrication equipment. -- Main reason for buying is notinformation processing.  *[Peter Marwedel, T. U. Dortmund]*

**Definition 2:** Embedded software is software integrated with physical processes.  The technical problem is managing time and concurrency in computational systems.  *[Edward A. Lee,  U. C. Berkeley]*

The first definition draws a wider boundary for the embedded systems category whereas the second definition states that integration with physical processes is required for a system to be considered as embedded.  We can argue for example, whether the cellular phones are embedded systems or not.  If we were asking this question in 1990s, then the answer would more likely to be "yes".  Early cellular phones were 100% phones mostly dealing with challenges of receiving and transmitting audio signals in real-time.  Most of the today's cellular phones however, are equipped with several additional features, so that sometimes less than 10% of the price paid goes into the phone functionality.  The remaining 90% buys a color screen, a camera, an Internet browser, an audio/video player, and more.  A $499 cellular phone sold today is an embedded system according to the first definition.  According to the second definition, it is only 10% embedded system where the remaining 90% can be categorized as a miniaturized PC. The second definition is a more precise categorization of embedded systems as they are seen from the electrical engineering perspective.  **Within the scope of this course, an embedded system is a device that answers the design challenges related to one or more of the characteristics** listed in the following section.  It is not really important how many of these design challenges we must deal with while working on a project.  The hardware and software development methods discussed in this course are all helpful design techniques whether we call the end product an embedded system or not.


Embedded System Characteristics
---------------------------------

Common examples of embedded systems include processors used in manufacturing equipment, transportation vehicles, telecommunication equipment, and consumer electronics.  Following are the characteristics of these systems:[#f3]_.

#. **Interaction with the physical processes:**  Sensors acquiring information about processes and actuators (or actors) controlling those processes are the connections between the most common embedded systems and the outside physical world.  Signal conditioning, data acquisition, driver electronics and transducers are important parts of these systems.
#. **Real-time constraints:**  Failure to complete system tasks within a specified time frame may result in harm to the user or degradation of system performance.  Such failures may be life-threatening in transportation vehicles.  This is a consequence of the embedded systems' interaction with the physical world. Performance of a PC is not critical while editing a document or browsing Internet.  A user can spend 100 seconds waiting in front of a PC where it may take only 5 seconds to complete the same task in ideal operating conditions.  An embedded system controlling a process on the other hand, must execute the specified computations with the required frequency and precision.  We can find average performance of a PC satisfactory even if it appears to be too slow from time to time.  Unlike a typical PC application, averaged performance is not a meaningful measure when we consider real-time systems.  An embedded system must work within specifications every time, all the time, and its performance must be guaranteed without statistical arguments.
#. **Reliability:**  Embedded systems can be safety-critical as another natural consequence of being connected to the physical environment.  Following are the major requirements of a reliable system.

   - **Availability:**  An embedded system must work within specifications as long as the system power is on.  Keeping wide tolerance limits (temperature, supply voltage, noise immunity, etc.) and implementing the necessary failure recovery procedures provide a high availability.
   - **Handling exceptions:**  Failure of a temperature sensor on a boiler should not cause a blast.  A dirty speed sensor should not result in a runaway wheel in the ABS system of a vehicle.  All similar failures of system components must be handled in the least harmful way.
   - **System start-up and recovery:**  Initial start-up and recovery after a power failure should be safe and consistent.
   - **Security:**  If necessary, access to the system controls must be restricted to the authorized users.  User data should be kept confidential whenever it is required and external communications should be protected.

#. **Efficiency:**  The following measures can be used for evaluating the efficiency of embedded systems:

   - **Power consumption:**  Many embedded systems run on batteries or they may rely on limited supplies provided through other system components.
   - **Code efficiency:**  In most cases the entire code of an embedded system is stored with the system.  The code-size to implement the required functionality should be as small as possible especially for the systems to be manufactured in large quantities.
   - **Run-time efficiency:**  The minimum amount of resources should be used for implementing the required functionality.  We should be able to meet time constraints using the least amount of hardware resources and energy. Weight, compactness, and manufacturing costs can be the other efficiency factors in embedded systems applications.  Weight of a mobile consumer electronics device may not be critical, but usually there are strict limits for the systems used on aircrafts and military equipment.

#.  **Dedicated application:**  Embedded systems are dedicated towards a certain application.  User interfaces are also specialized and optimized for that application.

Applications
=================

The following list summarizes the key areas in which embedded systems are used:

* **Industrial process control:**  Control systems used in manufacturing environments are the most typical examples of embedded systems.  Reliability of these systems is critical.
* **Household and consumer electronics:**  Home appliances, air conditioning equipment, and home security systems are the growing application areas for embedded systems.  Simple electrical or electromechanical control units have been replaced by more reliable and efficient digital controllers.

  Consumer electronics which constitutes the major part of the electronics industry relies on embedded processors.  The processing capability integrated into video and audio equipment is growing progressively with addition of high-performance digital signal processing (DSP) and memory units.  Mobile phone manufacturers have been pushing DSP and storage capabilities to the limits set by the power efficiency requirements in a very competitive market.
* **Medical equipment:**  Medical equipment used for diagnostic, therapeutic, and surgical purposes in hospitals have always been a traditional application area for embedded systems.  Home-care medical equipment and wearable devices or implants for patient monitoring and therapy purposes have growing potential for improving the quality of medical care.  Information processing power combined with the data storage and remote communication capabilities makes the embedded systems critical components of all modern medical devices .
* **Automotive electronics:**  Cars and trucks that have been sold during the last few decades contain a number of electronic control units.  A few examples are engine and emission control systems, air bag controllers, anti-lock braking systems (ABS), navigation computers with GPS.  Reliability of these systems are critical for user safety.  Embedded systems also have an important place in other transportation vehicles such as trains and all kinds of boats where user safety is critical.
* **Avionics equipment:**  All airplanes used for civil aviation or military purposes rely on electronic control and safety systems most of which can be classified as embedded systems.  An essential fraction - in some cases more than half - of the total cost of airplanes goes into the electronic components.
* **Robotics:**  Embedded systems are the essential parts of robots or robotic actuators as a natural consequence of their interaction with the physical environment.
* **Military applications:**  In many sections of the electronics industry, military applications led the development of electronic systems mainly due to the availability of financial resources for R&D.  Embedded systems have been used heavily in the military equipment for telecommunication, navigation, remote sensing, targeting, and several other applications

Future
=============

The cost of digital electronic components have been decreasing steadily while the integration density is rising as a result of well-known trends in the semiconductor manufacturing industry.  Digital systems replaced their analog or electromechanical counterparts mainly because of their reliability and flexibility.  In addition, advancements in the electronic assembly and packaging technologies facilitated miniaturization of devices such as the medical implants that seemed impossible to produce ten years ago.  Most people encounter embedded processors in their daily lives more often than they can realize.  There may be several appliances with embedded processors in our homes, and our cars are likely to contain tens of microprocessors depending on their age and luxury level.

Some market analysts predict that the embedded system market will be much larger than the market for PCs and similar systems in the near future.  It has been estimated that nearly 80% of the processors manufactured today are used in embedded systems [#f2]_ .In terms of variety of the components available in the market, most of the microcontrollers or embedded processors contain 8-bit processors.  On the other hand, 75% of all 32-bit processors are integrated into embedded systems.  Also, the complexity of embedded software is expected to increase, doubling the length of code every two years in the area of consumer electronics.
The marketing estimates on the number of processors are dictated by the expectations of the consumer electronics market.   Most consumer electronics products, such as mobile phones and portable audio/video players, heavily focus on the information processing capabilities.  These are high-volume products, so that the sales of every designed system can easily reach thousands or millions.  The number of embedded system designs in the other application areas is higher, but the sales of these products is much lower compared to consumer electronics products.  From a design engineer's point of view, future trends in embedded systems should not be tied solely to the development of processors.  Development of new sensor and actuator technologies and driver electronics will result in a growing number of applications for embedded systems.

Microcontroller (UC), Microprocessor, Integrated Circuit (IC)
---------------------------------------------------------------
**Integrated Circuit (IC):** (sometimes called a chip or microchip) a semiconductor wafer on which thousands or millions of tiny resistors, capacitors, and transistors are fabricated. An IC can function as an amplifier, oscillator, timer, counter, computer memory, or microprocessor. A particular IC is categorized as either linear (analog) or digital, depending on its intended application.

**Microprocessor:** An IC which has only the CPU inside them i.e. only the processing powers such as Intel’s Pentium 1,2,3,4, core 2 duo, i3, i5 etc. These microprocessors don’t have RAM, ROM, and other peripheral on the chip. A system designer has to add them externally to make them functional. Application of microprocessor includes Desktop PC’s, Laptops, notepads etc.

It should be noted that although a microprocessor requires external memory to operate, modern microprocessors often incorporate internal memory (known as cache) in order to execute operations more efficiently. In practice many modern developments challenges the traditional definitions of the various parts of a computer system.

**Microcontroller (UC):** A system of a microprocessor that is packaged with RAM, program storage and interface (I/O) circuitry to make it simple to use.  They're most used in (you guessed it) control applications. They are designed to perform specific tasks. Specific means applications where the relationship of input and output is defined.

**Embedded System:** The product that uses a microprocessor (or microcontroller) as a component. It consists of both hardware and software.

.. figure:: ../../../external/fig/EmbeddedSystems.png
          :alt: Embedded system block diagram
          :align: center


Also there are some other early-fundamental tools that can be comparable with microcontrollers:

**Programmable Logic Controller (PLC):** is a specialized industrial computer. It is custom programmed to monitor input signals (digital or analog), perform logical operations, and trigger specific output signals. PLCs are known to be rugged and are commonly used in extreme industrial environments or applications that have almost no room for failure. PLCs are popular because of their modular structure. This makes them easy to install in a plug-and-play manner [#f4]_.

**Field Programmable Gate Array (FPGA):** can be defined as a *kind of* microprocessor which doesn’t have any hardwired logic blocks because that would defeat the field programmable aspect of it. An FPGA is laid out like a net with each junction containing a switch that the user can make or break. This determines how the logic of each block is determined. Programming an FPGA involves learning HDL or the Hardware Description Language; a low level language that some people say to be as difficult as assembly language [#f5]_. Compared to FPGAs, microprocessors have fixed instructions.


Microprocessor Processing Structure
=======================================

.. figure:: ../../../external/fig/Batman.png
          :alt: Batman is awesome!
          :align: center

All of you know the stereotype: "computers work with ones and zeros". When I was in the first grade of the engineering class, whenever some bighead told me this sentence, I always wanted to kick their face with a spade. *Everyone* knows that a computer works with ones and zeros but really, *how?*

Well, so far what we know that a processor consists of millions of transistors. It is obvious that switching on/off transistors creates those *famous* ones and zeros. But again, how when you type :code:`printf("Hello world!");` on a strange window (we will call it later IDE), thousands of transistor switches on and off in a couple of nanoseconds and a **Hello world!** appears on another strange window (typically called a Console)?

There are many levels of nested knowledge which is required to fully grasp all the details of what is going on here. Furthermore a lot of software developers live happily without an understanding of the complete picture. Still some knowledge is required, especially for the times when things are not working they way you expect.

Compilation
---------------

The program you write usually contains at least two types of instructions. First it typically contains instruction to import other code in the form of libraries which are prepared for your convenience. Secondly your code, and the libraries contains instructions (formally known as statements) which are to be interpreted by the compiler.

..
   Let me introduce you: `Compiler <https://www.wikiwand.com/en/Compiler>`_. 

The compiler is a computer program which translates computer code written in one programming language (the source language) into another language (the target language). There are two versions of your program: the one you wrote but a computer can't read (source code) [#f8]_, and the magically generated one that a computer can read (machine code). 

..
   Except, a better than magic!

The following figure attempts to illustrate this process:
   
.. figure:: ../../../external/fig/Compiler.png
          :alt: Compiler illustration
          :align: center

..
   A compiler fills the gap between a human readable code and a machine readable code. Let's dive into the lovely princaple of a compiler (and assembler).
   Basically, a CPU (or microprocessor) can do a small number of things. They can read from /write to the memory and do some basic math operations (+ - * / & | ~ ^ << >>).

And this is the reason why your 32-bit programs don't work on a 64-bit computer (or they are needed to be installed into System32 folder etc). Because the controller divides all those set of 10101101's into the parts that the architecture supports. If the processors are different or the operating systems are different, then they use different machine instructions. Today things works slightly different to overcome these compatibility issues but we are not in computer architecture class now so better to stop here.


Further down the path of development the generated machine code is transferred to the microcontroller memory. When the microcontroller is started it will read and execute one instruction at a time, while traversing step by step through the memory. Special instruction are also able to instruct the controller to jump to different areas of the memory, allowing the program to take different paths depending on some external factors.

.. figure:: ../../../external/fig/microprocessororganization.png
          :alt: Microprocessor organization
          :align: center

In practice there is usually an intermediate step between the source code, and the machine code. The intermediate step is known as the assembly code, and is a low level language where each statement corresponds to a single operation which is directly supported by the CPU. For some special cases where high performance is needed, or special features not available in the programming language are needed, assembly language is used. It is also used extensively by experienced developers while debugging code, as it is not always obvious how the compiler interpret and translates our instructions.

Traditionally all software was developed using assembly language, but higher level languages (such as C/C++) have been designed to simplify the development process for all but the most advanced or special cases.


..
   How it was like in microprocessor world in the past? That is this famous **assembly** code that many old-genius engineers talk about and at the end of the discussion there is always someone says "but it is really hard". 

|pic1| |pic2| |pic3|

.. |pic1| image:: ../../../external/fig/assembly1.png
   :width: 33%

.. |pic2| image:: ../../../external/fig/assembly2.png
   :width: 33%

.. |pic3| image:: ../../../external/fig/assembly3.png
   :width: 33%


**Why assembly language?**

* It gives us direct access to machine instructions that we cannot use in high-level languages.
* It can be the best (only) way to generate efficient code in terms of speed and memory usage.
* It provides a better insight in to what is actually going on inside the computer (or microcontroller)
* It allows us to exploit hardware features not available in the programming language. One typical example is context switching of threads in a operating system.
* Provides a deeper understanding of how the system is operating. Invaluable when it comes to understanding the low level details of how the computer operates.

..
   ***Personal opinion:** it doesn't worth today.*
   Eirik disagree, but I agree that it is not worth it for a basic course such as ELE102

The embedded system is a bottomless well. It is impossible to cover everything in this course - and we don't need to. Embedded system world is a charming nerdy black hole that pulls you inside even more as you are willing to learn more. Therefore we limit have to limit for this course at some point.

For the youtubers:  `How do computers read code? <https://www.youtube.com/watch?v=QXjU9qTsYCc>`_ , The Evolution Of CPU Processing Power `Part 1 <https://www.youtube.com/watch?v=sK-49uz3lGg&t=315s>`_ `Part 2 <https://www.youtube.com/watch?v=kvDBJC_akyg>`_ `Part 3 <https://www.youtube.com/watch?v=NTLwMgak3Fk>`_ .




.. rubric:: Footnotes
.. [#f8] Strictly speaking the computer also reads the source language, but this reading process is more indirect. There is another set of instructions (another program, the compiler) running on the CPU which reads the code. The finalized program will be directly readable by the CPU without this intermediate step. Additionally for our purposes the compiler runs on your PC, while the finished program runs on the microcontroller.



Arduino
============

Open Source electronic prototyping platform based on flexible easy to use hardware and software.

.. figure:: ../../../external/fig/Arduino.png
          :alt: The original Arduino UNO board
          :align: center

The Arduino family consists of a large number of boards with different sets of features. Depending on your requirements, you should select the board which best fits the intended application. It can however be a good idea to avoid controllers which exceed the performance requirements. Especially if you work in a big company which has a mass production, even a couple of cents of saving in production can make a huge difference. Thus you should choose the simplest controller which meets the requirements.

In this course, we will not dwell to much on this aspect, the board is already selected for you (the Arduino UNO), and we are going to explore the features available.

.. 
   and we will not get into the details of embedded systems but I want you to focus on the "efficiency" of your codes and *designs* when you are still in the very beginning of the road.

..
   There is one important thing about embedded systems that you cannot stick on one microcontroller if you want to be a good embedded programmer.

Efficiency in code is always important, i.e. the code should be written in a way which as efficiently as possible achieve the intended goal. In a microcontroller program it can often be vital, due to the limited resources available (e.g. limited memory, and CPU clock speed). Luckily Arduino is plenty powerful for what we are going to achieve in this course, but just keep this in mind!

.. figure:: ../../../external/fig/ArduinoFamily.png
          :alt: Various boards in the Arduino family
          :align: center


Before Arduino and similar boards appeared on the marked, embedded programming had a pretty high entry barrier. It was hard for beginner to get started. You had to consider many different external circuits, clocks, prescalers, pin assignments, direct register access among other things. After Arduino entered the market, a new era has started.

..
   After Arduino has entered the market, there is a new way of learning was discovered on the microcontrollers side.
   used to seems like a rocket science. Literally, no kidding. Because it was still not *fun enough*, used to require more qualification.

A quick google search reveals that there is a lot more interest in Arduino, than it is in the microcontroller on which the original Arduino is based:

.. figure:: ../../../external/fig/ArduinoVSAtmega.png
          :alt: Arduino vs Atmega on Google
          :align: center

Some people may say that "Arduino is a toy", well, in a way it is true. Depends on how you use it. Our computers are also a toy right? It has all the functionalities of a mid-advanced microcontrollers have. One thing to note though, is that the low entry barrier has allowed many poorly skilled programmers to write about their experiences on the web. Beware of shitty tutorials, which teach shitty programming style, and low performance electronics.

Fun fact about Arduino:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Atmega328P is a microcontroller originally developed by a company called `Atmel <https://www.wikiwand.com/en/Atmel>`_. Atmel was bought by Microchip in 2016, and thus the company no longer exists. The microcontroller architecture particularly used by Atmel is called `AVR <https://www.wikiwand.com/en/AVR_microcontrollers>`_ developed by Alf-Egil Bogen and Vegard Wollan while they where students at NTNU in Trondheim. AVR derives its name from its developers and stands for Alf-Egil Bogen Vegard Wollan RISC microcontroller, although attempts have been made to re-define it as Advanced Virtual `RISC <https://www.wikiwand.com/en/Reduced_instruction_set_computer>`_ [#f6]_ . It has a `modified Harvard architecture <https://www.wikiwand.com/en/Modified_Harvard_architecture>`_ . 



Programming languages
---------------------

There are at least four (likely more) options for programming languages for programming the Arduino UNO.

* Assembly
* C
* C++
* Rust

For the remainder of these notes we will be using C++, which is the language officially supported by the Arduino developers. Still it is useful to have some knowledge of the alternatives. C++ is to a large extent an extension to the C programming language, with extra features related to object oriented programming. The reader should note though, that there is nothing C++ can do which you can not do in plain C. C++ offers extra features to make some programming approaches more convenient, while it also imposes some restrictions to make it slightly more difficult to make some common mistakes.

The Rust programming language differs a lot from the other two, although the syntax is inspired by the syntax found in C/C++. The development of Rust began in 2006, while C development began in 1972 with C++ released in 1983. Rust is a modern alterative which promises to solve some of the major challenges with the old low level languages, especially related to memory safety. It's popularity has increased in recent years, and the support for various microcontrollers is increasing. Again it should be noted that Rust does not provide us with the ability to create functionality which is impossible in C/C++, but it attempts to reduce the likelihood of common mistakes, and make the development process more convenient.



.. rubric:: References
.. [#f1] http://en.wikipedia.org/wiki/Microcontroller - 17 October 2019.
.. [#f2] Marwedel, Peter,  "Embedded System Design"  Springer, Boston, MA,  2006.
.. [#f3] Izmir Institute of Technology - Department of Electrical and Electronics Engineering *EE443 - Embedded Systems lecture notes - 2013*
.. [#f4] https://resources.altium.com/pcb-design-blog/plc-vs-embedded-system-when-you-should-choose-a-plc-despite-the-higher-cost-per-unit - 14 January 2020
.. [#f5] https://www.element14.com/community/groups/fpga-group/blog/2018/02/22/comparing-an-fpga-to-a-microcontroller-microprocessor-or-an-asic - 14 January 2020
.. [#f6] https://www.elprocus.com/difference-between-avr-arm-8051-and-pic-microcontroller/ - 14 January 2020.
