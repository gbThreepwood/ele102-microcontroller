:orphan:

.. _L12_spi_i2c:

******************
SPI and I2C
******************


    
Inter Integrated Circuit (I2C)
=====================================

.. warning:: pin numbers, where to use, SDA/SCL, advantages, history

I2C or :math:`I^2C` is a acronym for Inter integrated circuit, and is a serial communication bus standard released by Phillips in 1982. Initially intended for internal communication inside electronic appliances. It requires two wires SDA (Serial data), and SCL (Serial clock) in addition to a common ground.

The maximum physical separation between two deviced utilizing the bus is not defined in the standard, but a practical might be around 1 to 10 meters, depending on the baud rate. Longer lengths may work, but you may experience problems with reliability.

The System Management Bus (SMBus), defined by Intel in 1995, is a subset of :math:`I^2C`, defining additional features, such as automatic address configuration.

.. figure:: ../../fig/i2c_master_slave_interconnection.png
    :scale: 100

I2C operation
-------------

I2C uses a 7-bit address (it also supports 10-bit address), which theoretically allows 128 unique addresses (In practice only 127 because address 0b0000000 is reserved for a general call). Common :math:`I^2C` bus speeds include the 100 kbit/s standard mode, and the 400 kbit/s Fast mode.


The communication is initiated by the master, which sends a START condition followed by the 7-bit address and a single bit determining if it is a read (1) or a write (0) request. The START condition is signaled by allowing the data line (SDA) to go from high to low, while the clock line (SCL) remains high. The STOP condition is signaled by allowing the data line to go from low to high, while the clock line is high.

.. figure:: ../../fig/i2c_address_packet_format.png
    :scale: 100


System Management Bus (SMBus)
-----------------------------

The system managemen bus is an extension to I2C which has some useful additional features. In particular it supports a address resolution protocol for automatic slave identification, useful for plug and play support of external devices. It also has a feature known as the Host Notify protocol which allows a slave to initiate communication (the slave temporary becomes the master), and notify another device of some event.

I2C in Arduino
--------------

The standard library to use for I2C communication on the Arduino is the Wire library. The details on the functions in this library is available in the `Wire documentation <https://www.arduino.cc/en/reference/wire>`_

Connecting two Arduino's using I2C
--------------------------------------

The following example shows how to connect two Arduino UNO's using the I2C bus, each device will send anything it receives on the UART to the other device. One of the devices is operating in master mode, while the other is operating in slave mode. A slave can not initiate communication, but has to wait for the master to request data.

.. figure:: ../../fig/dual_arduino_i2c_communication_bb.png
    :scale: 50


Master
~~~~~~

.. literalinclude:: ../../../projects/i2c/simple_i2c_master/src/main.cpp
    :language: c


Slave
~~~~~

.. literalinclude:: ../../../projects/i2c/simple_i2c_slave/src/main.cpp
    :language: c


Reading and writing external EEPROM on I2C
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: The students do not have access to this kind of chip. Should we remove this section? Alternatively we could write a program that emulates an external EEPROM, and the students could work in groups, wher one arduino is reading the EEPROM of the other.


Serial Peripheral Interface (SPI)
=====================================
pin numbers, where to use, daisy-chain, advantages, history
Use your thesis

SPI (Serial Peripheral Interface Bus) is a synchronous serial communication interface developed by Motorola. It requires (at least) four wires:

* Serial Clock (SCLK)
* Master Output Slave Input (MOSI)
* Master Input Slave Output (MISO)
* Slave select (SS).

The slave select signal is used by the master to activate a given slave. Thus each slave requires a unique slave select wire.

SPI requires more wires than I2C, but it has some other advantages. In particular it supports higher data rate, and duplex communication (master and slave can send data at the same time). The maximum clock frequency supported by I2C on the Atmega 328 is 400kHz, while SPI supports 4Mhz.

SPI operation
-------------

The following figure depicts the operation of the SPI.

.. figure:: ../../fig/spi_master_slave_interconnection.png
    :scale: 100

The master initiates communication with a given slave by pulling the slave select pin low. The slave and master both prepare the data to be transmitted in the shift registers. The master generates the required clock signals in order to interchange the data between the two shift registers.

In master mode on the Atmega 328 the interface is implemented in such a way that the clock generator start automaically, when a byte is written to the shift register. The generated clock makes sure that a single byte is shifted, and then the clock signal is disabled.

The MISO pin on the slave is in tri-state mode as long as the Chip Select (SS) pin is high. This allows several slaves to share the same MISO wires.

The receive system is double buffered in order to allow time for the controller to read a byte from the buffer while the next byte is beeing received. The transmit system however is single buffered, which means that you must wait for the transmit cycle to complete before writing a new byte to the buffer.


SPI in Arduino
--------------

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


