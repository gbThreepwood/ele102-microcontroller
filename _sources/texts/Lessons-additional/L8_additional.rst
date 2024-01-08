*******************************************************
Additional material for lesson 8
*******************************************************


LCD and Interrupt Class Activity Codes
======================================

.. Here are the codes of the class activity. The aim is to get rid of the :code:`delay(ms)` and use the time in a wiser way. The example is taken from `EEenhusiast <https://eeenthusiast.com/arduino-delay-function-tutorial-on-software-interrupts-timer-library-alternatives-to-delay/>`_. So let's get started.

We have LCD circuit already built from the previous examples. We also attached a potentiometer to pin :code:`A0`. We would like to read the analog value from this potentiometer and display in on LCD immediately. 

In the working principle of the LCD, you have to :code:`print` the characters and :code:`clear` the LCD afterwards. Otherwise, you will have a blended display. You can try it out by yourself only with :code:`lcd.print()` and removing the :code:`lcd.clear()` in your code.

There is one important point in printing/clearing the LCD. If you clear the display so quickly, then you have a flicker problem. That's why, it is better to wait a little before clearing the screen for a better display quality. The following code is the simplest way to do it.

.. literalinclude:: ../../../projects/LCD_Interrupts_1/LCD_Interrupts_1.ino
    :language: c 

This code works perfectly in such a simple program. However, delays are always enemies of bigger programs. :code:`delay(ms)` stops all other executions, such as pin read/write, calculations, communications with the other sensors etc. Basically it halts the whole process in the specified period of time. That's why delays are one of the biggest problems in efficiency.

Insted of using a :code:`delay(ms)` function, we can use :code:`millis()` function. We can set a time interval and execute the routine in everr interval of time. Here is the same code without using any delay function.

.. literalinclude:: ../../../projects/LCD_Interrupts_2/LCD_Interrupts_2.ino
    :language: c 

Now, you can execute any other processes in your main function without adding an extra waiting time.

However, this is still not the efficient way. There is another process that takes a lot of time in the main process. We can do in in another way since there is a hidden "delay" or a "wait" in this process. Yes, I am talking about :code:`analogRead(A0)`. 

ADC is a pretty slow process compated to many other processes that you execute on a microcontroller. Actually, you don't have to wait the whole time of the conversion. You can instead set an ISR (Interrupt Service Routine) and pull the data in a specified period of time. The next code shows how it is done.

.. literalinclude:: ../../../projects/LCD_Interrupts_3/LCD_Interrupts_3.ino
    :language: c 


As you can see, we moved the ADC process in an ISR function. We call this ISR function in every 100000 microseconds (as we specified in the :code:`setup()` function.

Yet, there is one more step to increase efficiency here. We can merge the wait time for LCD to clear and the conversion time for the analogRead. If we get rid of the waiting setup with the millis() function and move this process intp the ISR, we can achive this objective. Here is the code for that.

.. literalinclude:: ../../../projects/LCD_Interrupts_4/LCD_Interrupts_4.ino
    :language: c 


Here you see a long long delay. This is only to demonstrate other processes that you may do inside your main function. It wouldn't be nice to leave the main function completely empty but it doesn't have any purpose at all. Also, see that the :code:`delay(ms)` function is only be cut off with an interrupt routine.

