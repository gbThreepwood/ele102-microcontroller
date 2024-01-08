:orphan:

Sound
========

Specifically, middle A ( a musical note) is 440 Hz.



So all we need to do is set up our speaker pin, and then raise and lower the voltage on that pin 440 times a second. Remember at the beginning of this chapter where we figured out that we need to have the speaker on (and then off) for 1136 microseconds. timeDelay = 1/(2* toneFrequency)

.. code-block:: c

    const int kPinSpeaker = 9;
    const int k_timeDelay = 1136;

    void setup()
    {
        pinMode(kPinSpeaker, OUTPUT);
    }

    void loop()
    {
        digitalWrite(kPinSpeaker, HIGH);
        delayMicroseconds(k_timeDelay);
        digitalWrite(kPinSpeaker, LOW);
        delayMicroseconds(k_timeDelay);
    }


:code:`tone(pin, frequency, duration)`

Note: (A strange warning. When the tone() function is running, PWM (pulse width modulation that we used in section 3.1.2) won’t run on pin 3 and pin 11. So if you are using a speaker in your sketch, you might want to avoid using those pins as PWM entirely.) 1 You may hook a speaker up to any of the pins. Here is an example to try on your Arduino: (Ok, so it is a simple C scale and
probably not really music.)

.. code-block:: c

    #define NOTE_C4 262
    #define NOTE_D4 264
    #define NOTE_E4 330
    #define NOTE_F4 349
    #define NOTE_G4 392
    #define NOTE_A4 440
    #define NOTE_B4 494
    #define NOTE_C5 523

    const int kPinSpeaker = 9;

    void setup()
    {
        pinMode(kPinSpeaker, OUTPUT);
    }

    void loop()
    {
        tone(kPinSpeaker, NOTE_C4, 500);
        delay(500);
        tone(kPinSpeaker, NOTE_D4, 500);
        delay(500);
        noTone(kPinSpeaker);
    }



The only thing here you haven’t seen before is #define. #define is a search and replace command to the computer during compilation. Any time it finds the first thing (up to a space), it replaces it with the rest of the line. 2 So in this example, when the computer finds NOTE_E4, it replaces it with a value of 330.



Keep it here fore tone function:

.. code-block:: c

    #include <Servo.h>

    Servo myServo;
    const int buzzerPin = 5;

    void setup() {
    Serial.begin(9600);
    myServo.attach(9);
    pinMode(buzzerPin, OUTPUT);
    }

    void loop() {
    int potVal = analogRead(A0);
    Serial.println(potVal);
    int pwmVal = map(potVal, 0, 1023, 0, 180);
    myServo.write(pwmVal);
    int toneVal = map(potVal, 0, 1023, 260, 550);
    tone(buzzerPin, toneVal);

    delay(10);

    }


