# Vending Machine Controller

## Introduction

In this practice you must recreate a vending machine with an Arduino Uno and its respective sensors and actuators.

## Materials

- Arduino UNO  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/af5a10fd-bcca-4646-8af1-613579511da2" width="300" height="300"></td>
  </tr>
</table>

- LCD  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/02938c23-334a-4311-b808-5e83b2128a09" width="300" height="300"></td>
    <td><img src="https://github.com/user-attachments/assets/f906e8d0-4b57-4c95-81ba-31c7d59b4143" width="300" height="300"></td>
  </tr>
</table>

- Joystick  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/37aa586f-18eb-46c9-a7c7-acec81a06708" width="300" height="300"></td>
    <td><img src="https://github.com/user-attachments/assets/755a6da3-f12e-40e3-b591-687ee63f45e0" width="300" height="300"></td>
  </tr>
</table>

- Sensor Temperatura/Humedad DHT11  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/3cb1fca2-0210-4146-abbd-aa666f7e24a8" width="300" height="300"></td>
    <td><img src="https://github.com/user-attachments/assets/44904ff6-04bc-40f6-9a4e-c5fdfa48ed17" width="300" height="300"></td>
  </tr>
</table>

- Sensor Ultrasonidos  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/64b4fe06-987d-4956-ac40-ee4ce0bd9059" width="300" height="300"></td>
    <td><img src="https://github.com/user-attachments/assets/1a22d451-d1a6-4ca1-ade2-00a285da430b" width="300" height="300"></td>
  </tr>
</table>

- Botón  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/91eb513a-53fa-49a8-8d88-974309320450" width="300" height="300"></td>
    <td><img src="https://github.com/user-attachments/assets/0b18b136-4ad6-42a2-9f44-a43e66279649" width="300" height="300"></td>
  </tr>
</table>

- 2 LEDs normales (LED1, LED2)  
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/942926c2-39e7-45fe-810a-d2b68b570139" width="300" height="300"></td>
    <td><img src="https://github.com/user-attachments/assets/a465c14a-8c1f-4296-9aa1-a89e6cf7f203" width="300" height="300"></td>
  </tr>
</table>

## Fritzing diagram

These are the pins used by the different sensors and actuators

<img src="https://github.com/user-attachments/assets/a61cd8b6-95a2-4da7-81e8-bc404a506fd0" width="500" height="500">


This is my circuit diagram

<img width="2589" height="1740" alt="circuito_p3_bb" src="https://github.com/user-attachments/assets/8687b25c-a925-45a0-a13e-af1257960d5b" />

## Explanation

### Interruptions

Interrupts allow the microcontroller to immediately respond to external events, without constantly checking (polling) its state inside the main loop().

```c
attachInterrupt(digitalPinToInterrupt(bot), button_pressed, CHANGE);
```
This line sets up an interrupt on the bot pin that calls the button_pressed() function whenever the pin state changes 


```c
if (digitalRead(bot) == HIGH) {
  // Button released
  if (pressing_time >= 5) {
    // Long press → enter admin mode
  } else if (pressing_time >= 2 && pressing_time < 3 && state == 1) {
    // Medium press → restart normal flow
  }
  pressing_time = 0;
} else {
  // Button pressed
  Timer1.start(); // Start counting how long the button is held
}
```
This detects if the button was released (HIGH) or pressed (LOW) and responds accordingly.

```c
Timer1.initialize(1000000); // Every 1 second
Timer1.attachInterrupt(timerCallback);
Timer1.stop(); // It only starts when button is pressed
```
When the button is pressed, the timer is started (Timer1.start()).
Every second, the timerCallback() function is triggered:

```c
void timerCallback() {
  noInterrupts();
  pressing_time++;
}
```
This increases pressing_time each second the button is held down, allowing the code to determine whether it was a short, medium, or long press.

### Watchdog

The Watchdog is used as a safety mechanism to reset the microcontroller if something goes wrong while handling the physical button. 
 
```c
void button_pressed() {
  interrupts();
  wdt_enable(WDTO_8S);
```
The Watchdog is enabled when the button (bot) is pressed (detected via interrupt).
It acts as a safety net in case the system gets stuck while handling the button

```c
if (digitalRead(bot) == HIGH) {
    wdt_disable(); // Disables the Watchdog if everything went well
    Timer1.stop();
```

If the button was released (HIGH), it means the logic continued correctly.
Therefore, the Watchdog is disabled, since no reset is needed.

```c
} else {
    interrupts();
    wdt_enable(WDTO_8S);
    Timer1.start();
}
```
If the button is still pressed (LOW), Timer1 is started and the Watchdog is re-enabled as a precaution.

## Joystick

For ease of gripping the joystick, the X and Y axes are inverted.

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/a120b557-15ee-43cd-9553-27ad24300fcb" width="400" height="400"></td>
    <td><img src="https://github.com/user-attachments/assets/9b93142b-8e3e-48c2-aafd-67d8f5561a5d" width="400" height="400"></td>
  </tr>
</table>

## Final result

Here is the explanatory video:

Google drive video URL:

Here's another video showing it working without cuts:

https://github.com/user-attachments/assets/89312ebd-874e-46b5-8c1c-645c70dfefee

Google drive video URL: https://drive.google.com/file/d/1w2Mf1H2V1m0FCMnVGkjwB-wjVSJ8c5rN/view?usp=sharing












