# Smart-Queue-Distance-Monitor

A simple ESP32 project that helps maintain personal space in lines and crowded areas!

> **Note:** This project was designed, wired, and simulated online using **Cirkit Designer**.

---

## What is this project?

Basically, it’s an automatic distance checker built with an **ESP32**. It uses a **PIR motion sensor** to detect when someone walks up, and an **ultrasonic sensor** to measure how close they are standing. 

If someone gets too close (closer than 50 cm), a red LED turns on and a buzzer starts beeping to politely tell them to step back!

---

## Why build it?

When people stand in line at checkout counters or crowded hallways, they often get too close without realizing it. Having someone manually manage lines takes time and effort. This little setup automates the process and gives instant feedback so people keep a safe distance on their own.

---

## Hardware & Pinout 

| Component | ESP32 Pin | Function |
| :--- | :--- | :--- |
| **PIR Sensor** | GPIO 13 | Detects motion in front of the queue |
| **Ultrasonic Trigger (Trig)** | GPIO 5 | Sends out the ultrasonic pulse |
| **Ultrasonic Echo (Echo)** | GPIO 18 | Measures reflected signal timing |
| **Buzzer** | GPIO 19 | Audible alert trigger |
| **Green LED** | GPIO 21 | Safe distance indicator (> 50 cm) |
| **Red LED** | GPIO 35 | Warning indicator (< 50 cm) |

<img width="1039" height="591" alt="smart queue" src="https://github.com/user-attachments/assets/28fca4b5-2b5c-48ba-bf5b-0a998b92fe87" />




https://github.com/user-attachments/assets/223c5e2a-04d3-4f1c-a60c-156ddd677e4b


## C++ Code

```cpp
int pirPin = 13;
int trigPin = 5;
int echoPin = 18;
int buzzerPin = 19;
int greenLed = 21;
int redLed = 35;

void setup() {
  Serial.begin(115200);

  pinMode(pirPin, INPUT);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(greenLed, OUTPUT);
  pinMode(redLed, OUTPUT);

  digitalWrite(greenLed, HIGH);
  digitalWrite(redLed, LOW);
  digitalWrite(buzzerPin, LOW);
}

void loop() {
  int val = digitalRead(pirPin);

  if (val == HIGH) {
    digitalWrite(trigPin, LOW);
    delayMicroseconds(2);
    digitalWrite(trigPin, HIGH);
    delayMicroseconds(10);
    digitalWrite(trigPin, LOW);

    long time = pulseIn(echoPin, HIGH, 30000);
    long dist = time * 0.034 / 2;

    if (time == 0) {
      dist = 999;
    }

    Serial.print("Distance: ");
    Serial.println(dist);

    if (dist > 0 && dist < 50) {
      digitalWrite(greenLed, LOW);
      digitalWrite(redLed, HIGH);
      digitalWrite(buzzerPin, HIGH);
      delay(100);
      digitalWrite(buzzerPin, LOW);
      delay(100);
    } else {
      digitalWrite(greenLed, HIGH);
      digitalWrite(redLed, LOW);
      digitalWrite(buzzerPin, LOW);
    }
  } else {
    digitalWrite(greenLed, HIGH);
    digitalWrite(redLed, LOW);
    digitalWrite(buzzerPin, LOW);
  }

  delay(200);
}

