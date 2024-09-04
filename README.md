# Home-automation-system-using-iot
This project is used for the security purpose and safe you from the fire Sparke  or something happen its gives you a waring message to your phone and you can be aware of the disaster. 
#include <Wire.h> 
#include <LiquidCrystal_I2C.h> 
#include "DHT.h" 
#include <SoftwareSerial.h> 
#define GSM_RX 11 
#define GSM_TX 12 
#define SMOKE_SENSOR A0 
#define WATER_LEVEL_SENSOR A1 
#define FIRE_SENSOR 4 
#define DHTPIN 3  
#define BUZZER 10 
#define SMOKE_LED 9 
#define TEMP_LED 8 
#define HUMIDITY_LED 7 
#define FIRE_LED 6 
#define FLOOD_LED 5 
#define DHTTYPE DHT11 
36 
#define THRESHOLD_SMOKE 60  
#define THRESHOLD_WATER 60  
#define TEMPERATURE_THRESHOLD 40  
#define HUMIDITY_THRESHOLD 70  
LiquidCrystal_I2C lcd(0x27, 16, 2);  
DHT dht(DHTPIN, DHTTYPE); 
SoftwareSerial gsm(GSM_RX, GSM_TX); 
void setup() { 
pinMode(SMOKE_SENSOR, INPUT); 
pinMode(WATER_LEVEL_SENSOR, INPUT); 
pinMode(FIRE_SENSOR, INPUT); 
pinMode(BUZZER, OUTPUT); 
pinMode(SMOKE_LED, OUTPUT); 
pinMode(TEMP_LED, OUTPUT); 
pinMode(HUMIDITY_LED, OUTPUT); 
pinMode(FIRE_LED, OUTPUT); 
pinMode(FLOOD_LED, OUTPUT); 
dht.begin(); 
lcd.init(); 
lcd.backlight(); 
37 
38  
   
  gsm.begin(9600); 
} 
 
void loop() { 
  int temperature = dht.readTemperature(); 
  int humidity = dht.readHumidity(); 
  int smokeValue1 = analogRead(SMOKE_SENSOR); 
  int waterLeve1 = analogRead(WATER_LEVEL_SENSOR); 
  int fireValue = digitalRead(FIRE_SENSOR); 
  int smokeValue = map(smokeValue1,0,1024,0,99); 
   int waterLevel = map(waterLeve1,0,1024,0,99); 
  
  lcd.setCursor(0, 0); 
  lcd.print("T: "); 
  lcd.print(temperature); 
  lcd.setCursor(6, 0); 
  lcd.print("H: "); 
  lcd.print(humidity); 
  lcd.setCursor(12, 0); 
  lcd.print("F: "); 
  
39  
  
   
  lcd.setCursor(0, 1); 
  lcd.print("S: "); 
  lcd.print(smokeValue); 
  lcd.setCursor(6, 1);  
  lcd.print("W: "); 
  lcd.print(waterLevel); 
   
 
  
  if (smokeValue > THRESHOLD_SMOKE) { 
    digitalWrite(BUZZER, HIGH); 
    digitalWrite(SMOKE_LED, HIGH); 
    sendSMS("Alert: Smoke detected! Possible fire hazard."); 
  } else { 
    digitalWrite(BUZZER, LOW); 
    digitalWrite(SMOKE_LED, LOW); 
  } 
 
  if (waterLevel > THRESHOLD_WATER) { 
    digitalWrite(FLOOD_LED, HIGH); 
40  
    sendSMS("This is to inform you that a flood alert has been issued for your 
area. Please take necessary precautions and stay safe."); 
 
  } else { 
    digitalWrite(FLOOD_LED, LOW); 
  } 
 
  if (fireValue == LOW) { 
    digitalWrite(BUZZER, HIGH); 
    digitalWrite(FIRE_LED, HIGH); 
    lcd.setCursor(15,0); 
    lcd.print("1"); 
    sendSMS("Alert: Flame Detected!"); 
    
  } else { 
    digitalWrite(FIRE_LED, LOW); 
    lcd.setCursor(15,0); 
    lcd.print("0"); 
  } 
 
  
  if (temperature > TEMPERATURE_THRESHOLD) { 
41  
    digitalWrite(TEMP_LED, HIGH); 
    sendSMS("Temperature has exceeded the threshold."); 
     
 
  } else { 
    digitalWrite(TEMP_LED, LOW); 
  } 
 
  if (humidity > HUMIDITY_THRESHOLD) { 
    digitalWrite(HUMIDITY_LED, HIGH); 
    sendSMS("High Humidity Detected!"); 
   
  } else { 
    digitalWrite(HUMIDITY_LED, LOW); 
  } 
 
  delay(1000);  
} 
 
void sendSMS(String message) { 
  gsm.println("AT+CMGF=1");  
  delay(1000); 
gsm.println("AT+CMGS=\"+918778077054\"");  
delay(1000); 
gsm.print(message); 
delay(100); 
gsm.write(26); 
delay(1000);
