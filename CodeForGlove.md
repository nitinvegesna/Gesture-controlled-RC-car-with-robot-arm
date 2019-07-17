// Gesture-controlled-RC-car-with-robot-arm
#include <SPI.h>                      //the communication interface with the modem
#include "RF24.h"                     //the library which helps us to control the radio modem

int msg[4]; //Total number of data to be sent (data package)

//define the flex sensor input pins

int flex_1 = A0;
int flex_2 = A2;
int flex_3 = A3; 
int flex_4 = A5;

//define variables for flex sensor values

int flex_1_val;
int flex_2_val;
int flex_3_val; 
int flex_4_val;



RF24 radio(9,8);                     //9 and 8 are a digital pin numbers to which signals CE and CSN are connected.
                                      
const uint64_t pipe = 0xE8E8F0F0E1LL; //the address of the modem, that will receive data from Arduino.
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  radio.begin();
  // set the address
  radio.openWritingPipe(pipe);


  // Set module as transmitter
  // radio.stopListening();
}

void loop() {
  // put your main code here, to run repeatedly:

		// Read the flex sensor values
      flex_1_val = analogRead(flex_1);
      flex_2_val = analogRead(flex_2);
      flex_3_val = analogRead(flex_3);
      flex_4_val = analogRead(flex_4);

			// Use map and constrain functions to make flex sensors be in between certain range
      flex_1_val = map(flex_1_val, 500, 800, 0, 9);
      flex_1_val = constrain(flex_1_val, 0, 9);
      flex_2_val = map(flex_2_val, 500, 800, 0, 9);
      flex_2_val = constrain(flex_2_val, 0, 9);
      flex_3_val = map(flex_3_val, 500, 800, 0, 9);
      flex_3_val = constrain(flex_3_val, 0, 9);
      flex_4_val = map(flex_4_val, 500, 800, 0, 9);
      flex_4_val = constrain(flex_4_val, 0, 9); 
			
			// Set the data in the array equal to the flex sensor values. This will be the data sent to the receiver
      msg[0] = flex_1_val;
      msg[1] = flex_2_val;
      msg[2] = flex_3_val;
      msg[3] = flex_4_val;
 
  // Send message to receiver

      radio.write(&msg, sizeof(msg));
      delay(500);
			
			// Print the data sent to receiver in Serial monitor
      Serial.println(msg[0]);
      Serial.println(msg[1]);
      Serial.println(msg[2]);
      Serial.println(msg[3]);
