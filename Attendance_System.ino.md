**1(ARDUINO UNO R3 CODE )**



\#include <LiquidCrystal.h>

\#include <SoftwareSerial.h>



// LCD

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);



// Communication

SoftwareSerial link(A2, A1);



// Pins

const int potPin = A0;

const int rolePin = A3;

const int startButton = 7;

const int stopButton = 8;



// Variables

bool systemRunning = false;

bool autoMode = true;



int signalValue = 0;

int lastSentValue = -1;



unsigned long lastAutoTime = 0;

unsigned long lastManualTime = 0;



const unsigned long AUTO\_INTERVAL = 2000;

const unsigned long MANUAL\_INTERVAL = 100;



// Sequence number

uint16\_t sequenceNumber = 0;



// Button debounce

bool lastStartReading = HIGH;

bool lastStopReading = HIGH;



bool stableStartState = HIGH;

bool stableStopState = HIGH;



unsigned long startDebounceTime = 0;

unsigned long stopDebounceTime = 0;



const unsigned long debounceDelay = 40;



// LCD helper

void printLine(int row, const char \*message) {

&#x20; lcd.setCursor(0, row);



&#x20; int i = 0;



&#x20; while (message\[i] != '\\0' \&\& i < 16) {

&#x20;   lcd.print(message\[i]);

&#x20;   i++;

&#x20; }



&#x20; while (i < 16) {

&#x20;   lcd.print(' ');

&#x20;   i++;

&#x20; }

}



// Read filtered potentiometer

int readPotValue() {



&#x20; long total = 0;



&#x20; for (int i = 0; i < 8; i++) {

&#x20;   total += analogRead(potPin);

&#x20; }



&#x20; int average = total / 8;



&#x20; return map(average, 0, 1023, 1, 1200);

}



// Send signal

void sendSignal(int value) {



&#x20; sequenceNumber++;



&#x20; link.print("V,");

&#x20; link.print(sequenceNumber);

&#x20; link.print(",");

&#x20; link.println(value);



&#x20; signalValue = value;



&#x20; char line\[17];



&#x20; printLine(0, "SIGNAL VALUE:");



&#x20; snprintf(line, sizeof(line), "%4d  %s",

&#x20;          signalValue,

&#x20;          autoMode ? "AUTO" : "MANUAL");



&#x20; printLine(1, line);

}



// Start system

void startSystem() {



&#x20; systemRunning = true;



&#x20; link.println("RUN");



&#x20; lastAutoTime = millis();

&#x20; lastManualTime = millis();



&#x20; lastSentValue = -1;



&#x20; if (autoMode) {

&#x20;   signalValue = random(1, 1201);

&#x20; } else {

&#x20;   signalValue = readPotValue();

&#x20; }



&#x20; sendSignal(signalValue);

}



// Stop system

void stopSystem() {



&#x20; systemRunning = false;



&#x20; link.println("OFF");



&#x20; lastSentValue = -1;



&#x20; printLine(0, "SYSTEM STOPPED");

&#x20; printLine(1, "PRESS START");

}



// Button handling

void handleButtons() {



&#x20; unsigned long now = millis();



&#x20; // START BUTTON

&#x20; bool startReading = digitalRead(startButton);



&#x20; if (startReading != lastStartReading) {

&#x20;   startDebounceTime = now;

&#x20; }



&#x20; if ((now - startDebounceTime) > debounceDelay) {



&#x20;   if (startReading != stableStartState) {



&#x20;     stableStartState = startReading;



&#x20;     if (stableStartState == LOW) {



&#x20;       if (!systemRunning) {

&#x20;         autoMode = true;

&#x20;         startSystem();

&#x20;       } else {



&#x20;         autoMode = !autoMode;



&#x20;         if (autoMode) {

&#x20;           lastAutoTime = now;

&#x20;         } else {

&#x20;           lastManualTime = now;

&#x20;         }



&#x20;         lastSentValue = -1;



&#x20;         sendSignal(signalValue);

&#x20;       }

&#x20;     }

&#x20;   }

&#x20; }



&#x20; lastStartReading = startReading;



&#x20; // STOP BUTTON

&#x20; bool stopReading = digitalRead(stopButton);



&#x20; if (stopReading != lastStopReading) {

&#x20;   stopDebounceTime = now;

&#x20; }



&#x20; if ((now - stopDebounceTime) > debounceDelay) {



&#x20;   if (stopReading != stableStopState) {



&#x20;     stableStopState = stopReading;



&#x20;     if (stableStopState == LOW \&\& systemRunning) {

&#x20;       stopSystem();

&#x20;     }

&#x20;   }

&#x20; }



&#x20; lastStopReading = stopReading;

}



// AUTO MODE

void handleAutoMode() {



&#x20; unsigned long now = millis();



&#x20; if (now - lastAutoTime >= AUTO\_INTERVAL) {



&#x20;   lastAutoTime = now;



&#x20;   int newValue = random(1, 1201);



&#x20;   sendSignal(newValue);

&#x20; }

}



// MANUAL MODE

void handleManualMode() {



&#x20; unsigned long now = millis();



&#x20; if (now - lastManualTime >= MANUAL\_INTERVAL) {



&#x20;   lastManualTime = now;



&#x20;   int newValue = readPotValue();



&#x20;   if (newValue != lastSentValue) {

&#x20;     sendSignal(newValue);

&#x20;   }

&#x20; }

}



void setup() {



&#x20; pinMode(rolePin, INPUT);



&#x20; pinMode(startButton, INPUT\_PULLUP);

&#x20; pinMode(stopButton, INPUT\_PULLUP);



&#x20; lcd.begin(16, 2);



&#x20; link.begin(4800);



&#x20; randomSeed(analogRead(A5));



&#x20; if (digitalRead(rolePin) == LOW) {



&#x20;   printLine(0, "TRANSMITTER");

&#x20;   printLine(1, "PRESS START");



&#x20; } else {



&#x20;   printLine(0, "WRONG ROLE");

&#x20;   printLine(1, "CHECK A3 PIN");

&#x20; }

}



void loop() {



&#x20; // This code runs only on Arduino 1

&#x20; if (digitalRead(rolePin) != LOW) {

&#x20;   return;

&#x20; }



&#x20; handleButtons();



&#x20; if (!systemRunning) {

&#x20;   return;

&#x20; }



&#x20; if (autoMode) {

&#x20;   handleAutoMode();

&#x20; } else {

&#x20;   handleManualMode();

&#x20; }

}





**2.(ARDUINO UNO R3 CODE)**



\#include <LiquidCrystal.h>

\#include <SoftwareSerial.h>



// LCD

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);



// Communication

SoftwareSerial link(A2, A1);



// Pins

const int rolePin = A3;



const int greenLED = 9;

const int redLED = 10;

const int yellowLED = 13;



const int buzzer = 6;



// Variables

bool systemRunning = false;



int receivedValue = 0;



int currentCategory = 0;

int previousCategory = 0;



bool ledActive = false;



unsigned long ledStartTime = 0;



const unsigned long LED\_DURATION = 2000;



// Buzzer

int pendingBeeps = 0;



bool buzzerActive = false;



unsigned long buzzerTimer = 0;



const unsigned long BEEP\_DURATION = 90;

const unsigned long BEEP\_GAP = 110;



// Communication buffer

char inputBuffer\[32];

byte bufferIndex = 0;



uint16\_t lastSequence = 0;



// LCD helper

void printLine(int row, const char \*message) {



&#x20; lcd.setCursor(0, row);



&#x20; int i = 0;



&#x20; while (message\[i] != '\\0' \&\& i < 16) {

&#x20;   lcd.print(message\[i]);

&#x20;   i++;

&#x20; }



&#x20; while (i < 16) {

&#x20;   lcd.print(' ');

&#x20;   i++;

&#x20; }

}



// LED control

void turnOffLEDs() {



&#x20; digitalWrite(greenLED, LOW);

&#x20; digitalWrite(yellowLED, LOW);

&#x20; digitalWrite(redLED, LOW);



&#x20; ledActive = false;

}



// Category

int getCategory(int value) {



&#x20; if (value <= 300) {

&#x20;   return 1;

&#x20; }



&#x20; else if (value <= 699) {

&#x20;   return 2;

&#x20; }



&#x20; else {

&#x20;   return 3;

&#x20; }

}



// LED trigger

void triggerLED(int category) {



&#x20; turnOffLEDs();



&#x20; if (category == 1) {

&#x20;   digitalWrite(greenLED, HIGH);

&#x20; }



&#x20; else if (category == 2) {

&#x20;   digitalWrite(yellowLED, HIGH);

&#x20; }



&#x20; else if (category == 3) {

&#x20;   digitalWrite(redLED, HIGH);

&#x20; }



&#x20; ledActive = true;



&#x20; ledStartTime = millis();

}



// Buzzer queue

void addBeeps(int count) {



&#x20; pendingBeeps += count;



&#x20; if (pendingBeeps > 30) {

&#x20;   pendingBeeps = 30;

&#x20; }

}



// Non-blocking buzzer

void handleBuzzer() {



&#x20; unsigned long now = millis();



&#x20; if (!systemRunning) {

&#x20;   noTone(buzzer);

&#x20;   buzzerActive = false;

&#x20;   pendingBeeps = 0;

&#x20;   return;

&#x20; }



&#x20; if (buzzerActive) {



&#x20;   if (now - buzzerTimer >= BEEP\_DURATION) {



&#x20;     noTone(buzzer);



&#x20;     buzzerActive = false;



&#x20;     pendingBeeps--;



&#x20;     buzzerTimer = now;

&#x20;   }



&#x20; } else {



&#x20;   if (pendingBeeps > 0 \&\&

&#x20;       now - buzzerTimer >= BEEP\_GAP) {



&#x20;     tone(buzzer, 700);



&#x20;     buzzerActive = true;



&#x20;     buzzerTimer = now;

&#x20;   }

&#x20; }

}



// LED timer

void handleLEDTimer() {



&#x20; if (ledActive \&\&

&#x20;     millis() - ledStartTime >= LED\_DURATION) {



&#x20;   turnOffLEDs();

&#x20; }

}



// Display

void updateDisplay() {



&#x20; char line\[17];



&#x20; if (currentCategory == 1) {

&#x20;   printLine(0, "CATEGORY: LOW");

&#x20; }



&#x20; else if (currentCategory == 2) {

&#x20;   printLine(0, "CATEGORY: MODERATE");

&#x20; }



&#x20; else if (currentCategory == 3) {

&#x20;   printLine(0, "CATEGORY: HIGH");

&#x20; }



&#x20; snprintf(line, sizeof(line), "VALUE: %4d",

&#x20;          receivedValue);



&#x20; printLine(1, line);

}



// Process received value

void processValue(uint16\_t sequence, int value) {



&#x20; if (!systemRunning) {

&#x20;   return;

&#x20; }



&#x20; if (value < 1 || value > 1200) {

&#x20;   return;

&#x20; }



&#x20; if (sequence == lastSequence) {

&#x20;   return;

&#x20; }



&#x20; lastSequence = sequence;



&#x20; receivedValue = value;



&#x20; previousCategory = currentCategory;



&#x20; currentCategory = getCategory(receivedValue);



&#x20; // Every new value triggers LED

&#x20; triggerLED(currentCategory);



&#x20; // Beep only when category changes

&#x20; if (previousCategory != 0 \&\&

&#x20;     currentCategory != previousCategory) {



&#x20;   addBeeps(currentCategory);

&#x20; }



&#x20; updateDisplay();

}



// Process communication commands

void processMessage(char \*message) {



&#x20; if (strcmp(message, "RUN") == 0) {



&#x20;   systemRunning = true;



&#x20;   currentCategory = 0;

&#x20;   previousCategory = 0;



&#x20;   lastSequence = 0;



&#x20;   turnOffLEDs();



&#x20;   pendingBeeps = 0;



&#x20;   printLine(0, "SYSTEM STARTED");

&#x20;   printLine(1, "WAITING SIGNAL");



&#x20;   return;

&#x20; }



&#x20; if (strcmp(message, "OFF") == 0) {



&#x20;   systemRunning = false;



&#x20;   currentCategory = 0;

&#x20;   previousCategory = 0;



&#x20;   turnOffLEDs();



&#x20;   noTone(buzzer);



&#x20;   pendingBeeps = 0;

&#x20;   buzzerActive = false;



&#x20;   printLine(0, "SYSTEM STOPPED");

&#x20;   printLine(1, "WAITING START");



&#x20;   return;

&#x20; }



&#x20; if (message\[0] == 'V' \&\& message\[1] == ',') {



&#x20;   char \*firstComma = strchr(message, ',');

&#x20;   char \*secondComma = strchr(firstComma + 1, ',');



&#x20;   if (secondComma == NULL) {

&#x20;     return;

&#x20;   }



&#x20;   \*secondComma = '\\0';



&#x20;   uint16\_t sequence =

&#x20;     (uint16\_t)atoi(firstComma + 1);



&#x20;   int value = atoi(secondComma + 1);



&#x20;   processValue(sequence, value);

&#x20; }

}



// Read serial communication

void receiveData() {



&#x20; while (link.available()) {



&#x20;   char received = link.read();



&#x20;   if (received == '\\n') {



&#x20;     inputBuffer\[bufferIndex] = '\\0';



&#x20;     if (bufferIndex > 0) {

&#x20;       processMessage(inputBuffer);

&#x20;     }



&#x20;     bufferIndex = 0;

&#x20;   }



&#x20;   else if (received != '\\r') {



&#x20;     if (bufferIndex < sizeof(inputBuffer) - 1) {



&#x20;       inputBuffer\[bufferIndex++] = received;



&#x20;     } else {



&#x20;       bufferIndex = 0;

&#x20;     }

&#x20;   }

&#x20; }

}



void setup() {



&#x20; pinMode(rolePin, INPUT);



&#x20; pinMode(greenLED, OUTPUT);

&#x20; pinMode(yellowLED, OUTPUT);

&#x20; pinMode(redLED, OUTPUT);



&#x20; pinMode(buzzer, OUTPUT);



&#x20; lcd.begin(16, 2);



&#x20; link.begin(4800);



&#x20; turnOffLEDs();



&#x20; noTone(buzzer);



&#x20; if (digitalRead(rolePin) == HIGH) {



&#x20;   printLine(0, "RECEIVER READY");

&#x20;   printLine(1, "WAITING SIGNAL");



&#x20; } else {



&#x20;   printLine(0, "WRONG ROLE");

&#x20;   printLine(1, "CHECK A3 PIN");

&#x20; }

}



void loop() {



&#x20; // This code runs only on Arduino 2

&#x20; if (digitalRead(rolePin) != HIGH) {

&#x20;   return;

&#x20; }



&#x20; receiveData();



&#x20; handleLEDTimer();



&#x20; handleBuzzer();

}

