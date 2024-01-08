.. _audio_processing:

******************************************************************
Audio processing
******************************************************************

.. role:: ccode(code)
        :language: c

Using the ADC to read audio
==============================



Resistor ladder digital to analog converter
===========================================



Transmitting audio digitally through UART
=========================================

When transmitting data at a high rate it is preferable to use :ccode:`Serial.write()` instead of :ccode:`Serial.print()` if possible, as it does less processing in the background and thus it is much faster.

.. https://github.com/PowerBroker2/SerialTransfer