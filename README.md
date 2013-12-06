Our-Code
========

Our Code from Sprint 3 week 1

// Arduino PWM Speed Control：
// Pin definitions for PWM speed control using 
// DFRobot Arduino Compatible Motor Shield (2A):

//#define BOT_2011
#ifdef BOT_2011
#include "MotorRobotBase.h"
#define Motor MotorRobotBase
#else
#include "Motor.h" 
#endif

#include "Sensor.h"

#define FULL_SPEED 70
#define SLOW_SPEED  25
void go_right(Motor &m1, Motor &m2) 
{
  m1.reverse(SLOW_SPEED);
  m2.reverse(FULL_SPEED); 
}

void go_left(Motor &m1, Motor &m2) 
{
  m1.reverse(FULL_SPEED);
  m2.reverse(SLOW_SPEED);
}
void go_straight(Motor &m1, Motor &m2) 
{
  m1.reverse(FULL_SPEED);
  m2.reverse(FULL_SPEED);
}

void twist_right(Motor &m1, Motor &m2) 
{
  m1.reverse(FULL_SPEED);
  m2.forward(FULL_SPEED);
}

void twist_left(Motor &m1, Motor &m2) 
{
  m1.forward(FULL_SPEED);
  m2.reverse(FULL_SPEED);
}

void go_back(Motor &m1, Motor &m2) 
{
  m1.forward(FULL_SPEED);
  m2.forward(FULL_SPEED);
}

void go_back_left(Motor &m1, Motor &m2) 
{
  m1.forward(FULL_SPEED);
  m2.reverse(SLOW_SPEED);
}

void go_back_right(Motor &m1, Motor &m2) 
{
  m1.forward(SLOW_SPEED);
  m2.forward(FULL_SPEED);
}

Motor right_motor(Motor::MOTOR_1);
Motor left_motor(Motor::MOTOR_2);
Sensor left_light(Sensor::SENSOR_0);
Sensor right_light(Sensor::SENSOR_3);
int last_left;
int last_right;
int last_dark;
#define LEFT 1
#define RIGHT 2
int count;

void setup () 
{ 
  Serial.begin(9600);

  right_motor.setup();
  left_motor.setup();
  left_light.setup();
  left_light.set_mode(Sensor::SIMPLEX_MODE);
  left_light.calibrate();
  right_light.setup();
  right_light.set_mode(Sensor::SIMPLEX_MODE);
  right_light.calibrate();
} 

#define RT_SENSE 50
#define LT_SENSE 50
void loop () 
{
  char light_str[255];

  int left_light_level;
  int right_light_level;
  int left_delta;
  int right_delta;

  int right = 0;
  int left = 0;

  int right_brite = 0;
  int left_brite = 0;

  left_light.read(left_light_level);
  left_delta = left_light_level - left_light.get_light_level();

  right_light.read(right_light_level);
  right_delta = right_light_level - right_light.get_light_level();

  if (abs(left_delta) >= LT_SENSE) {
    left = 1;
    if (left_delta > 0) {
      left_brite = 1;
    }
  }
  if (abs(right_delta) >= RT_SENSE) {
    right = 1;
    if (right_delta > 0) {
      right_brite = 1;
    }
  } 

  do {
    if (!left && !right) {
      count++; 
      if (count%100 == 0) {
        left_light.calibrate();
        right_light.calibrate(); 
      }    
      go_straight(left_motor, right_motor);
      break;
    }
    count = 0;
    if (left && right) {
      if (LEFT == last_dark) {
        go_back_right(left_motor, right_motor);
        delay(400);
        go_right(left_motor, right_motor);
      } else {
        go_back_left(left_motor, right_motor);
        delay(400);
        go_left(left_motor, right_motor);
      }
      delay(200);
      
      break;
    }
    if (right && right_brite) {
      go_left(left_motor, right_motor);
      break;
    }
    if (left && left_brite) {
      go_right(left_motor, right_motor);
      break;
    }    
    
  } while(0);

  if (left && !last_left) {
     last_dark = LEFT;
  } else {
    last_dark = RIGHT;
  }
  

  last_left = left;
  last_right = right;

  delay(5);

  /*
  Serial.print(left_delta);
   Serial.print(":");
   Serial.print(left_light_level);
   Serial.print("\n");
   Serial.print(right_delta);
   Serial.print(":");
   Serial.print(right_light_level);
   Serial.print("\n");
   */


  /*
  if (light_level > 1000) {  
   Serial.write("1000\n");    
   } else  if (light_level > 900) {  
   Serial.write("900\n");    
   } else  if (light_level > 800) {  
   Serial.write("800\n");    
   } else  if (light_level > 700) {  
   Serial.write("700\n");    
   }  else  if (light_level > 600) {  
   Serial.write("600\n");    
   }  else  if (light_level > 500) {  
   Serial.write("500\n");    
   }  else  if (light_level > 400) {  
   Serial.write("400\n");    
   }  else  if (light_level > 300) {  
   Serial.write("300\n");    
   }  else  if (light_level > 200) {  
   Serial.write("200\n");    
   }  else  if (light_level > 100) {  
   Serial.write("100\n");    
   }   else  { 
   Serial.write("00\n");    
   } 
   */

}





