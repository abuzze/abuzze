---
title: "Arduino Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet', 'Arduino']
category: ['cheat sheet']
slug: 'cheat sheet'
---

# Arduino
## Arduino Nano
### Settings for Arduino IDE
Tools > Board > Arduino Nano
Tools > Processor > ATMEGA328P Old Bootloader
Tools > Programmer > ArdinoISP
Tools > Port > Select COMX (replug to check which port is new. It's never COM1)


## Simple Programs
### Onboard LED Blink
```cpp
int LED = 13;  //13 for L LED or 30 for RX LED
int DelayTime = 500;

void setup() {
pinMode(13, OUTPUT);
}

void loop() {
   digitalWrite(LED, HIGH);
   delay(DelayTime);
   digitalWrite(LED,LOW);
   delay(DelayTime);
}
```

### Buttontester
```cpp
// Blink the onboard LED, when a button is pressed

const int LED = 13;                       // sets LED to pin 13
const int DelayTime = 500;
const int buttonPin = 2;     // the number of the pushbutton pin

int buttonState = 0;

void setup() {
    pinMode(LED, OUTPUT);
    pinMode(buttonPin, INPUT_PULLUP);  //define INPUT_PULLUP to use the onboard resistor
}

void loop() {

  buttonState = digitalRead(buttonPin);
  if (buttonState == HIGH) {
      digitalWrite(LED,LOW); // LED ON
  }
  else{
      digitalWrite(LED,HIGH); // LED OFF
  }
}

```
### Simple Keyboard (Won't work with Nano. Needs an ATmega16U2)
```cpp
int buttonPin = 9;  // Set a button to any pin

void setup()
{
  pinMode(buttonPin, INPUT);  // Set the button as an input
  digitalWrite(buttonPin, HIGH);  // Pull the button high
}

void loop()
{
  if (digitalRead(buttonPin) == 0)  // if the button goes low
  {
    Keyboard.write('z');  // send a 'z' to the computer via Keyboard HID
    delay(1000);  // delay so there aren't a kajillion z's
  }
}
```