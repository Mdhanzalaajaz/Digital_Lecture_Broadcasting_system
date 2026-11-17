# Digital Lecture Broadcasting System

## Project Overview

The Digital Lecture Broadcasting System is an Arduino-based communication prototype designed to demonstrate digital signal transmission between a transmitter and a receiver.

The system supports AUTO and MANUAL operating modes and provides real-time signal monitoring through LCD displays, LED indicators, and buzzer alerts.

## Features

- Transmitter and Receiver communication using two Arduino Uno boards.
- AUTO mode for automatic signal generation.
- MANUAL mode for user-controlled signal generation.
- Real-time signal value monitoring.
- Dual LCD display system.
- Three-level signal classification:
  - LOW (1–300)
  - MODERATE (301–699)
  - HIGH (700–1200)
- LED status indicators.
- Buzzer alerts during category transitions.
- START and STOP controls.

## Hardware Components

- 2 × Arduino Uno
- 2 × 16x2 LCD Displays
- Potentiometer
- Green LED
- Yellow LED
- Red LED
- Piezo Buzzer
- Push Buttons
- Resistors
- Capacitors
- Breadboards and Jumper Wires

## Software and Technologies

- Arduino IDE
- C++ (Arduino Programming)
- Tinkercad Circuits
- SoftwareSerial Library
- LiquidCrystal Library

## Working Principle

The transmitter generates numerical signal values in AUTO or MANUAL mode. These values are transmitted to the receiver through serial communication.

The receiver displays the received values on its LCD and classifies them into LOW, MODERATE, and HIGH categories.

LED indicators represent the signal category, while the buzzer generates alerts whenever the category changes.

## Project Simulation

The complete prototype is designed and tested using Tinkercad Circuits.

## Project Status

Working Simulation Prototype.

## Author

Developed as an academic project.
