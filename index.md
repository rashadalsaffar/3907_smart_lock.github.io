## ELEC 3907 Engineering Project - Smart Lock

The code detailed below details the initialization of multiple modules used in the smart lock setup, including a Servo Motor, LCD screen, and temperature sensor.

#include <Wire.h>
#include <Servo.h>
#include <LiquidCrystal.h>
#include <TimeLib.h>
#define aref_voltage 3.3
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
Servo myservo;
int val;
char state = 0;
int tempPin = 1;
int tempReading;
void setup()
{
  analogReference(EXTERNAL);
  Serial.begin(9600);
  Wire.begin();
  lcd.begin(16, 2);
  lcd.print(" ELEC 3908 - 3D");
  lcd.setCursor(0, 1);
  lcd.print("   SMART LOCK");
  delay(2000);
  lcd.clear();
  setTime(10, 38, 30, 20, 2, 2018);
}


void loop()
{
  printClockLCD();
  lcd.setCursor(10, 1);
  tempReading = analogRead(tempPin);
  float voltage = tempReading * aref_voltage;
  voltage /= 1024.0;
  float temperatureC = (voltage - 0.5) * 100;
  lcd.print(temperatureC, 1);
  lcd.write(0xdf);
  lcd.print("C");
  myservo.attach(9);
  if (Serial.available() > 0) {
    state = Serial.read();
  }
  if (state == 'c') {
    val = 180;
  }
  if (state == 'cc') {
    val = 0;
  }
  if (state == 'off') {
    myservo.detach();
  }
  val = map(val, 0, 180, 0, 180);
  myservo.write(val);
  delay(1000);
}

void printClockLCD(void) {
  lcd.setCursor(0, 0);
  lcd.print(year());
  lcd.print("-");
  lcd.print(month());
  lcd.print("-");
  lcd.print(day());
  lcd.setCursor(0, 1);
  lcd.print(hour());
  printDigits(minute());
  printDigits(second());
}

void printDigits(int digits)
{
  lcd.print(':');
  if (digits < 10)
    lcd.print('0');
  lcd.print(digits);
}
