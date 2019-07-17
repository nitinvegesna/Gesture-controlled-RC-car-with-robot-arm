// Gesture-controlled-RC-car-with-robot-arm
// Include necessary libraries

#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>
#include <Servo.h>

// defining variables
const int leftSideSpeed = 2; 
const int leftForward = 5;
const int leftReverse = 7;
const int rightSideSpeed = 4;
const int rightForward = 6;
const int rightReverse = 3; 
bool enable = false; 
int pos=0;
// Create servo objects
Servo servo1;
Servo servo2;

// create an RF24 object
RF24 radio(9,8); // (CE, CSN)

// address through which the modules communicate

const uint64_t pipe = 0xE8E8F0F0E1LL; //the address of the modem that will receive data from the Arduino.

// creating data package consisting of 4 characters.
int msg[4];

void setup() {


  // put your setup code here, to run once:
// Specifying pins servos are connected to
  servo1.attach(10);
  servo2.attach(A5);
// Enabling Serial monitor
  while (!Serial);
    Serial.begin(9600);
  

radio.begin();

  // set the address
  radio.openReadingPipe(1, pipe);

  // set module as receiver
  radio.startListening();
}

void loop() {
  // put your main code here, to run repeatedly:

  // read the data if available in buffer
  if (radio.available())  {
    // char motor[32] = {0};
    radio.read(&msg, sizeof(msg));

	// If all flex sensors are in between a certain range, the enable changes.
    if ((msg[0] > 1) && (msg[0] < 9)  &&
        (msg[2] > 1) && (msg[2] < 9) && (msg[3] > 1) && (msg[3] < 9)) {
      digitalWrite (leftForward, LOW);
      digitalWrite (leftReverse, LOW);
      digitalWrite (rightForward, LOW);
      digitalWrite (rightReverse, LOW);
      Serial.print("Current enable is: ");
      Serial.println(enable);
      enable = !enable;    			// Changing enable
      Serial.print("New enable is: ");;
      Serial.println(enable);
      delay(5000);
    }
		
		// If enable is true, servos should be enabled
    if (enable == true) {
      Serial.println("Control Servos");
      
		// Makes servo open and close
      if (msg[0] > 1 && msg[0] < 9) {
        Serial.println("Servo 1");
        servo1.write(25);            //move the servo to 25 degrees
        delay(1500);                 //wait 1500 milliseconds
        servo1.write(155);           //move the servo to 155 degrees
        delay(1500); 
      }
		
		// Makes servo rotate
      else if (msg[2] > 1 && msg[2] < 9) {
        Serial.println("Servo 2");
        
        // Rotate the servo to 180 and back to 0 degrees
        for (pos = 0; pos <= 180; pos += 1)  {
          servo2.write(pos);
          delay (5);
        }

        for (pos = 180; pos >= 0; pos -= 1)  {
          servo2.write(pos);
          delay (5);
        }
      }
      servo2.write(0); 		// Servo should go back to 0 degrees
    }

		// If enable = false, motors should be enabled
    else if (enable == false) {
      servo1.write(0);
      servo2.write(0);
      Serial.println("Control Motors");

		// Makes car move forward
      if (msg[0] > 1 && msg[0] < 9) {
        Serial.println("Flex 1");
        digitalWrite (leftForward, LOW);
        digitalWrite (leftReverse, HIGH);
        digitalWrite (rightForward, LOW);
        digitalWrite (rightReverse, HIGH);
    
      }
    
		// Makes car move backward
      else if (msg[2] > 1 && msg[2] < 9) {
        Serial.println("flex_2");
        digitalWrite (leftForward, HIGH);
        digitalWrite (leftReverse, LOW);
        digitalWrite (rightForward, HIGH);
        digitalWrite (rightReverse, LOW);
  
      
      }
  	// Makes car turn right
      else if (msg[3] > 1 && msg[3] < 9) {
        Serial.println("flex_3");
        digitalWrite (leftForward, HIGH);
        digitalWrite (leftReverse, LOW);
        digitalWrite (rightForward, LOW);
        digitalWrite (rightReverse, LOW);
  
  
      }
  
		// Makes car turn left
      else if (msg[1] > 1 && msg[1] < 9) {
        Serial.println("flex_4");
        digitalWrite (leftForward, LOW);
        digitalWrite (leftReverse, LOW);
        digitalWrite (rightForward, HIGH);
        digitalWrite (rightReverse, LOW);
  
  
      }
      
    
		// Make the car stop
      else  {
        digitalWrite (leftForward, LOW);
        digitalWrite (leftReverse, LOW);
        digitalWrite (rightForward, LOW);
        digitalWrite (rightReverse, LOW);
        delay (500);

      }
    } 
    Serial.println(msg[0]); // Printing flex sensor 1 value 
    Serial.println(msg[1]); // Printing flex sensor 2 value 
    Serial.println(msg[2]); // Printing flex sensor 3 value 
    Serial.println(msg[3]); // Printing flex sensor 4 value 


}}
  
