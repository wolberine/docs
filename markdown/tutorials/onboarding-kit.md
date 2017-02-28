---
title: Onboarding kit
nav_sort: 1
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2017/02/onboarding-lesson1-wiring.png"
---

### Introduction
Hi and welcome to using the Hologram platform! This set of tutorials is meant to be used with the Onboarding Kit purchased through the store.  The purpose of these lessons is to demonstrate how you can robustly and securely build an IoT device with a few clicks and some lines of code.  The lessons walk users through much of the basic aspects of using the Hologram platform: from using Dash pin functionality all the way through sending an SMS to the Dash from your phone, parsing the message on the Dash, and sending data back to your phone.  Get excited!

#### Bill of Materials
The first thing we should do is make sure you have all the supplies you need for your Onboarding Kit.  You should have: 

* 1 button
* 1 LED
* 2 1.2 kohm resistors
* 2 male-female 3-wire bundles
* 7 male-male 3" wires
* 1 PIR sensor
* 1 DHT11 temperature sensor
* 1 Power brick
* 1 Dash Kit
    * Antenna
    * Dash
    * SIM
    * USB
* $5 promo balance on your account (comes free with your Onboarding Kit)

#### Prerequisites
This tutorial is not meant to teach people the ins and outs of working on embedded systems.  Rather, it's meant to get people who are already familiar with embedded systems (i.e. the Arduino) up and running quickly on the Hologram platform.  

Make sure you've already completed the Dash quickstart guide before beginning this tutorial.  This guide can be found at hologram.io/start.  Once you've completed this, come back and go through the lessons!

Remember, the full Hologram platform functionality (Dash, connectivity, APIs, and cloud) documentation can always be found on our main docs page: http://hologram.staging.wpengine.com/docs/.

### Lesson 1 - Blink an LED

Blinking an LED is quick and easy on the Dash.  It works just like blinking an
  LED on the Arduino.  The differences between blinking an LED on the Arduino
  and the Dash lie in the different capabilities of the pinouts on each
  platform.  The Dash has 36 pins that are capable of several functions.  See
  the diagram below for capabilities: (insert our wiring diagram)

The first step is to place your Dash on the breadboard and wire up the
  components.  See the Fritzing sketch below for a visual placement diagram.
  Please note the short edge of the LED is the negative terminal and should be
  connected to ground.

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson1-wiring.png"
    alt="Lesson 1 wiring diagram" }}}

Note: in sketches, all pins can be named via the generic left (L) and right
  (R) pin names (i.e. L18).  However D- pin names can only be configured as
  digital outputs, A- pin names can only be configured as analog outputs.
  
Once you're all wired up there are a couple of things to note.
    * In your setup function, the GPIO on the Dash needs to be set as an output pin. To blink the LED, we're setting digital pin D30 to an output.

```clike
pinMode(D30, OUTPUT);
```

Now that our pinMode is set as an output, we can write to set the pin output to be a high voltage (3.3V) or low voltage (0V, GND).

```clike
digitalWrite(D30, HIGH);
```

Putting it all together, we can copy and paste the full sketch to blink an LED on the Dash!

```clike
//Hologram Lesson 1
//This lesson teaches how to blink an LED on the Dash

void setup() {

  // put your setup code here, to run once:
  // initialize digital pin as an output.
  pinMode(D30, OUTPUT);

}

void loop() {

  // put your main code here, to run repeatedly:
  digitalWrite(D30, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);                       // wait for a second
  digitalWrite(D30, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);                       // wait for a second

}
```

### Lesson 2A - Read a Button Input

Since in the previous lesson we were able to use a digital output on the Dash,
it's now time to use a digital input.  The Onboarding Kit provides a button to
demonstrate reading a digital input.  Add a button and a (pull-up) resistor to
the breadboard created in Lesson 1.

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson2a-wiring.png"
    alt="Lesson 2a wiring diagram" }}}

Similar to setting a GPIO to be a digital input (like we did in Lesson 1), we
can set a GPIO to be a digital output.

```clike
pinMode(D19, INPUT);
```

For this lesson, we're going to toggle the LED.  To toggle properly we need to
debounce the button.  Debouncing decreases the likelihood that your
microcontroller will read false positives on the button input.  The general
method for debouncing a button is to check that there was a sufficiently long
time between registered button presses.

Therefore we set up a delay time in milliseconds:

```clike
int debounceDelay = 50;
```

We also register when the button was pressed and compare this time against the delay time.

```clike
if (buttonReading != lastButtonState) {
    lastDebounceTime = millis();
    lastButtonState = buttonReading;
}
```

```clike
if ((millis() - lastDebounceTime) > debounceDelay)
```

Finally, if the press was valid, we toggle the LED!

```clike
digitalWrite(D30, ledState);
```

The below sketch puts all the pieces together and gets your button to toggle the LED.

```clike
//Hologram Lesson 2A
//This lesson teaches how to read a button output.

int lastDebounceTime = 0;
int debounceDelay = 50;
int lastButtonState = 1;
int ledState = LOW;
int buttonReading = 0;

int buttonState = HIGH;

void setup() {
  
  // put your setup code here, to run once:

  // initialize digital pin LED_BUILTIN as an output.
  pinMode(D30, OUTPUT);
  pinMode(D19, INPUT);

}

void loop() {
  
  buttonReading = digitalRead(D19);
  
  if (buttonReading != lastButtonState) {
      lastDebounceTime = millis();
      lastButtonState = buttonReading;
  } 

  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (buttonState != lastButtonState) {
      buttonState = lastButtonState;
        if (buttonState == LOW) {
                 ledState = !ledState;
                 digitalWrite(D30, ledState);
          }
      }
   }

}
```

### Lesson 2B - Read a Sensor Input

This half-lesson shows how you can use a PIR sensor to detect motion.  We'll
see the PIR in action by reading the output of the PIR sensor  and if this
sensor detects motion, the LED will turn on.  The PIR sensor will continue to
be used in later lessons.

Build on the hardware from previous Lesson 2A by connecting the PIR input as
shown.  This sensor included in the Onboarding Kit requires a 5V supply
however the signal output to the Dash is 3.3V.  Therefore the PIR sensor is
powered using the 5V pass through on the Dash which is supplied from the USB
input.

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson2b-wiring.png"
    alt="Lesson 2b wiring diagram" }}}

Similar to previous lessons, the PIR signal input needs to be set.

```clike
pinMode(D09, INPUT);
```

As shown in the loop() below, we take the reading directly from the PIR sensor and use it to turn on the LED.

```clike
//Hologram Lesson 2B
//This lesson uses a PIR sensor to turn on an LED

int pirReading = 0;

void setup() {

  // put your setup code here, to run once:

  // initialize digital pin LED_BUILTIN as an output.
  pinMode(D30, OUTPUT);
  pinMode(D09, INPUT);

}

void loop() {

  pirReading = digitalRead(D09);
  digitalWrite(D30, pirReading);

}
```

Note: This PIR sensor has the signal and supply voltage lines fixed.  Need to get this fixed before live


### Lesson 3A - Build a Local Motion Detection System

Now that we've built a system to include a button, an LED, and a PIR sensor,
it's time to use them all together to make something fully functional.  We'll
use this set-up to build a motion detection system (i.e. a security system).
The logic will work as follows:

* System begins “UNARMED”
    * Unarmed means PIR sensor input is ignored
    * The LED turns on and off every 100ms
* System becomes “ARMED” 5 seconds after the button is pressed
    * The system will print to the Serial terminal “Button was pressed. System will be armed in 5 seconds.”
    * Once the button is pressed, the LED will go dark
    * After 5 seconds, the system is fully armed and the LED will be solid on
    * If the PIR sensor detects motion, the system will become Unarmed and print to the Serial terminal “Intruder detected!  System unarmed”

In the code, we're adding a couple of features. We have a state machine that
flips between “ARMED” and “UNARMED” states which are defined in the top of the
code sample.  For ease of coding, we'll use ARMED and UNARMED in our code while
letting the Arduino compiler know these two states can be mapped to integers 1
and 2 for any comparison operations

```clike
#define ARMED 1
#define UNARMED 2
```

To easier view what the system is doing, we'll print out system transition
statements to the local Serial port.  The Serial port is initialized in the
setup() portion of our code. 

```clike
Serial.begin(9600);
```

Then the Serial port can be printed to with simple commands. In future lessons
we'll use similar notation to print to the Hologram Cloud where the data will be
accessible from anywhere.

```clike
Serial.println("Button was pressed.  System will be armed in 5 seconds.");
```

To get the new system working, go ahead and paste the code snippet into the Arduino IDE and try it out!

```clike
//Hologram Lesson 3A
//This lesson turns the Lesson 2 system into a local motion detection security system

#define ARMED 1
#define UNARMED 2

int lastDebounceTime = 0;
int debounceDelay = 50;
int lastButtonState = 1;
int ledState = LOW;
int buttonReading = 0;
int pirReading = 0;
int motionDetect = LOW;
int alarmState = UNARMED;

int buttonState = HIGH;

void setup() {
  
  // put your setup code here, to run once:

  // initialize digital pin LED_BUILTIN as an output.
  pinMode(D30, OUTPUT);
  pinMode(D19, INPUT);
  pinMode(D09, INPUT);

  Serial.begin(9600);

}

void loop() {

 //blink LED if alarm is unarmed.  arm alarm by pressing the button
 if (alarmState == UNARMED) {

    digitalWrite(D30, HIGH);   // turn the LED on (HIGH is the voltage level)
    delay(100);                       // wait for a second
    digitalWrite(D30, LOW);    // turn the LED off by making the voltage LOW
    delay(100);                       // wait for a second

    buttonReading = digitalRead(D19);

    if (buttonReading != lastButtonState) {
      lastDebounceTime = millis();
      lastButtonState = buttonReading;
  }

  //the button toggles security system ARMED (1) or UNARMED (0)
    if ((millis() - lastDebounceTime) > debounceDelay) {
      if (buttonState != lastButtonState) {
        buttonState = lastButtonState;
        if (buttonState == LOW){
          alarmState = ARMED;
          Serial.println("Button was pressed.  System will be armed in 5 seconds.");
        }
        delay(5000);
        }
     }
 }

 if (alarmState == ARMED) {
  digitalWrite(D30, HIGH);
  pirReading = digitalRead(D09);
  if (pirReading == HIGH){
    alarmState = UNARMED;
    Serial.println("Intruder detected! System unarmed.");
  }
 }

}
```

### Lesson 3B - Turn Your Local Security System into a Cloud-Connected System

In this lesson, we'll introduce the cellular capabilities of the Dash.  These
features allow you to send messages from your local Dash up to the Hologram
Cloud.  The cloud can be used for storage, to export data, and even to route
data to external services via webhooks or SMS.

To send data to the Hologram Cloud, we need to add a similar of code to the
Serial terminal in the setup() portion of the sketch

```clike
HologramCloud.begin();
```

 Then we can use the HologramCloud.sendMessage function to send data in String
 or integer (ask Erik for the capabilities) form to the cloud.  This function
 call allows you to send both your message and add meta data to your message
 with a tag.  Below, our message is “Intruder Detected!” with the tag “ALARM”.

```clike
HologramCloud.sendMessage("Intruder Detected!", "ALARM");
```

In the HologramCloud class there's also a catch for any messages that don't make
it to the cloud on the first try.  Messages may not make it to the Hologram
Cloud due to changes in cellular coverage in your local area.  Therefore, we can
set up a retry loop to keep sending until the message is confirmed received.

```clike
while(!messageSuccess && whileCounter--){
  messageSuccess = HologramCloud.sendMessage("Intruder Detected!", "ALARM");
}
```

Now it's time to combine these new cloud send features with your previous
lesson!  Copy the below code into the Arduino IDE and try the system out
yourself.  Be sure the check your data logs for the messages appearing in the
cloud.

```clike
//Hologram Lesson 3B
//This lesson turns on cloud messages from the Dash

#define ARMED 1
#define UNARMED 2

int lastDebounceTime = 0;
int debounceDelay = 50;
int lastButtonState = 1;
int ledState = LOW;
int buttonReading = 0;
int pirReading = 0;
int motionDetect = LOW;
int alarmState = UNARMED;
int buttonState = HIGH;
int messageSuccess = 0;
int whileCounter = 5;

void setup() {

  // put your setup code here, to run once:

  // initialize digital pin LED_BUILTIN as an output.
  pinMode(D30, OUTPUT);
  pinMode(D19, INPUT);
  pinMode(D09, INPUT);

  Serial.begin(9600);

  HologramCloud.begin();

}

void loop() {

 //blink LED if alarm is unarmed.  arm alarm by pressing the button
 if (alarmState == UNARMED) {

    digitalWrite(D30, HIGH);   // turn the LED on (HIGH is the voltage level)
    delay(100);                       // wait for a second
    digitalWrite(D30, LOW);    // turn the LED off by making the voltage LOW
    delay(100);                       // wait for a second

    buttonReading = digitalRead(D19);

    if (buttonReading != lastButtonState) {
      lastDebounceTime = millis();
      lastButtonState = buttonReading;
  }

  //the button toggles security system ARMED (1) or UNARMED (0)
    if ((millis() - lastDebounceTime) > debounceDelay) {
      if (buttonState != lastButtonState) {
        buttonState = lastButtonState;
        if (buttonState == LOW){
          alarmState = ARMED;
          Serial.println("Button was pressed.  System will be armed in 5 seconds.");
        }
        delay(5000);
        }
     }
 }

 if (alarmState == ARMED) {
  digitalWrite(D30, HIGH);
  pirReading = digitalRead(D09);
  if (pirReading == HIGH){
    alarmState = UNARMED;
    Serial.println("Intruder detected! System unarmed.");
    
    messageSuccess = HologramCloud.sendMessage("Intruder Detected!", "ALARM");
    //retry if message doesn't succeed
    while(!messageSuccess && whileCounter--){
      messageSuccess = HologramCloud.sendMessage("Intruder Detected!", "ALARM");
    }
  }
 }
}
```

Once your system is running, test out arming the device and triggering the PIR
sensor.  Then check out the data on the cloud by logging, finding your SIM, and
checking the data logs.  You should see something similar to this!

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson3b-cloud.png"
    alt="Cloud message" }}}

### Lesson 4 - Control Your Dash via SMS

Now that we have outbound messages working from the Dash, we can do two really cool things with the Hologram Cloud
  * Use the Cloud route text messages to your phone (or this could be a webhook to any external service)
  * Use your personal phone to control your security system's status and also report back the status

First, let's configure the webhook for routing any outbound Dash messages to your phone

* Go to dashboard.hologram.io
* Click on Routes on the left hand side of the page
* Click on Add New Route on the bottom of the page
* Select route SMS
* Choose your own route nickname (optional)
* Subscribe to “ALARM”
* Publish to your personal phone number (replace 1234567890 with your own phone number)

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson4-sms.png"
    alt="SMS configuration" }}}

Now, let's configure the Cloud to allow your Dash to receive inbound text
messages from your phone.  To do this we need to register a phone number for
your Dash

* Go to dashboard.hologram.io
* Find and click on your SIM card that's in your Dash
* Click on Cloud Configuration
* Click Purchase a Phone Number
* Choose your local country and area code (with the purchase of your Onboarding Kit your account should have a promo balance on it that covers this cost)

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson4-phone-number.png"
    alt="Phone number configuration" }}}

On the Dash side of things, we need to be able to receive an SMS and parse it.
SMS's inbound to the Dash can be caught and parsed by a handler.  To initialize
this handler we add the following line to our setup() function where cloud_sms
will be the name of the handler function which parses the SMS

```clike
HologramCloud.attachHandlerSMS(cloud_sms); 
```

In the loop() portion of the code, we need to poll for any inbound cloud messages events 

```clike
HologramCloud.pollEvents();
```

And finally, we need to parse the SMS.  The handler can be declared as follows
(going to need Erik's help here...) with a pointer to any sms_event object.

```clike
void cloud_sms(const sms_event *sms)
```

We can access and print out the SMS message contents by dereferncing the sms pointer.

```clike
  Serial.println("CLOUD SMS RECEVIED:");
  Serial.print("SMS SENDER: ");
  Serial.println(sms->sender);
  Serial.print("SMS TIMESTAMP: ");
  Serial.println(sms->timestamp);
  Serial.println("SMS TEXT: ");
  Serial.println(sms->message);
```

Then, we can provide logic to either set the system as ARMED, UNARMED, or send a
status update to the cloud which routes to your phone

```clike
  if (message == "ARM") {
    alarmState = ARMED;
    Serial.print("Alarm state is: ");
    Serial.println(alarmState);
  }

  if (message == "DISARM") {
    alarmState = UNARMED;
    Serial.print("Alarm state is: ");
    Serial.println(alarmState);
  }
  if (message == "STATUS"){
    Serial.println("Sending status...");
    if (alarmState == ARMED){
      HologramCloud.sendMessage("System is currently armed", "ALARM");
    }
    else {
      HologramCloud.sendMessage("System is currently unarmed", "ALARM");
    }
  }
```

If we put all these pieces together we can now:

* Arm and disarm the security system via text message
* Receive updates on intruders and status of the system via text message
* Arm and disarm the system locally with a button input and motion movement

It's time to try out the whole system together!

```clike
//Hologram Lesson 4
//ARM, DISARM via SMS and send status with STATUS

#define ARMED 1
#define UNARMED 2

int lastDebounceTime = 0;
int debounceDelay = 50;
int lastButtonState = 1;
int ledState = LOW;
int buttonReading = 0;
int alarmState = UNARMED;
int buttonState = HIGH;

void cloud_sms(const String &sender, const rtc_datetime_t &timestamp, const String &message) {
  Serial.println("CLOUD SMS RECEIVED:");
  Serial.print("SMS SENDER: ");
  Serial.println(sender);
  Serial.print("SMS TIMESTAMP: ");
  Serial.println(timestamp);
  Serial.println("SMS TEXT: ");
  Serial.println(message);

  if (message == "ARM") {
    alarmState = ARMED;
    Serial.print("Alarm state is: ");
    Serial.println(alarmState);
  }

  if (message == "DISARM") {
    alarmState = UNARMED;
    Serial.print("Alarm state is: ");
    Serial.println(alarmState);
  }
  if (message == "STATUS") {
    Serial.println("Sending status...");
    if (alarmState == ARMED) {
      HologramCloud.sendMessage("System is currently armed", "ALARM");
    }
    else {
      HologramCloud.sendMessage("System is currently unarmed", "ALARM");
    }
  }

  Serial.println("Leaving SMS handler...");

}

void setup() {
  // put your setup code here, to run once:

  // initialize digital pin LED_BUILTIN as an output.
  pinMode(D30, OUTPUT);
  pinMode(D19, INPUT_PULLUP);
  pinMode(D09, INPUT_PULLDOWN);

  Serial.begin();
  HologramCloud.attachHandlerSMS(cloud_sms);
}

void loop() {
  //blink LED if alarm is unarmed.  arm alarm by pressing the button
  if (alarmState == UNARMED) {

    //blink the LED
    digitalWrite(D30, HIGH);   // turn the LED on (HIGH is the voltage level)
    delay(100);                       // wait for a second
    digitalWrite(D30, LOW);    // turn the LED off by making the voltage LOW
    delay(100);                       // wait for a second

    buttonReading = digitalRead(D19);

    if (buttonReading != lastButtonState) {
      lastDebounceTime = millis();
      lastButtonState = buttonReading;
    }

    //the button toggles security system ARMED (1) or UNARMED (0)
    if ((millis() - lastDebounceTime) > debounceDelay) {
      if (buttonState != lastButtonState) {
        buttonState = lastButtonState;
        if (buttonState == LOW) {
          alarmState = ARMED;
          Serial.println("Button was pressed.  System will be armed in 5 seconds.");
        }
        delay(5000);
      }
    }
  }

  if (alarmState == ARMED) {
    digitalWrite(D30, HIGH);
    int pirReading = digitalRead(D09);
    if (pirReading == HIGH) {
      int whileCounter = 5;
      alarmState = UNARMED;
      buttonReading = digitalRead(D19);

      Serial.println("Intruder detected! System unarmed.");
      bool messageSuccess = HologramCloud.sendMessage("Intruder Detected!", "ALARM");

      while (!messageSuccess && whileCounter--) {
        Serial.println("Retrying message send...");
        messageSuccess = HologramCloud.sendMessage();
      }

      if(messageSuccess)
        Serial.println("Message transmitted successfully.");
      else
        Serial.println("Message transmission failed.");

      lastButtonState = 0;
      buttonReading = 0; //reset button inputs

    }
  }
}
```

Similar to before, check your cloud dashboard for data logs from your Dash.
Also progress through arming and disarming the system via SMS commands. 

{{{ image src="/wp-content/uploads/2017/02/onboarding-lesson4-sms-received.png"
    alt="SMS conversation" }}}

Congratulations!  You've finished your Onboarding Kit tutorial.  You built a
hardware system from the ground up and explored functionality all the way from
blinking an LED to controlling a microcontroller via SMS.  That's seriously a
cool accomplishment.  For more Dash and Hologram Cloud functionality, check out
our docs [here](/docs/).  If you have any questions or comments, contact
us!

