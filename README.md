# STM32-Based-smart-greenhouse-project


#include <LiquidCrystal.h>
#include <DHT.h>
LiquidCrystal lcd(PB10,PB11,PA0,PA1,PA2,PA3);
#define DHTPIN PA8
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);
int seviye1= 600;
int seviye2= 300;
int seviye3= 800;
int ldr= PA6;
int ldrDurum;
int tempGoal =20;
int suseviye= PA5;
int suveri;
int topraknem= PA4;
int toprakveri;
int fan= PB6;
int irLed= PB13;
int led = PB15;
int ledV=0;
int suLed = PB14;
int sumotoru= PB12;
int fanhiz=0;
int ledparlaklik=0;
int temp;
int irDurum=0;
int previousMillis = 0;
int interval = 5000;
int tNem = 0;
void setup() {
  dht.begin();
  lcd.begin(16, 2);
  pinMode(ldr, INPUT);
  pinMode(suseviye, INPUT);
  pinMode(topraknem, INPUT);
  pinMode(fan, OUTPUT);
  pinMode(led, OUTPUT);
  pinMode(irLed, OUTPUT);
  pinMode(suLed, OUTPUT);
  pinMode(sumotoru, OUTPUT);
  pinMode(PC13, OUTPUT);
  //tempGoal = dht.readTemperature();
  tempGoal =23;
  digitalWrite(fan, 1);
}
void loop() {
 temp = dht.readTemperature();
 int hum = dht.readHumidity();

 ldrDurum=analogRead(ldr);
 suveri=analogRead(suseviye);
 toprakveri=4095-analogRead(topraknem);
 tNem=(toprakveri*100)/4095;
 //su deposu//
 if(suveri>1200){
  digitalWrite(suLed, LOW);
 
 }else{
  digitalWrite(suLed, HIGH);
 }
//**kullanıcı modu **//
//if(switch1Durum == LOW && switch2Durum==LOW){
lcd.clear();
lcd.setCursor(0,0);
lcd.print("temp:");
lcd.print(temp);
lcd.print("c");
lcd.setCursor(9, 0);
lcd.print("Hum:%");
lcd.print(hum);
lcd.setCursor(0, 1);
lcd.print("IR:");
lcd.print(irDurum);//(irLed*100)/4095);
lcd.setCursor(5,1);
lcd.print("F:");
lcd.print(fanhiz);
lcd.setCursor(10, 1);
lcd.print("T:%");
lcd.print(tNem);
delay(100);
//if(temp > tempGoal+5)
//fanhiz =1;
//else if(temp =< tempGoal-5)
//fanhiz =0;
//digitalWrite(fan, fanhiz);
if(temp >= tempGoal+2){
  irDurum = 1;
  digitalWrite(irLed, irDurum);
  fanhiz =1;
  digitalWrite(fan, fanhiz);
}
else if(temp <= tempGoal-2){
  irDurum = 0;
  digitalWrite(irLed, irDurum);
  fanhiz =0;
  digitalWrite(fan, fanhiz);
}else{
  irDurum = 1;
  digitalWrite(irLed, irDurum);
  fanhiz =0;
  digitalWrite(fan, fanhiz);
}
//**led parlaklik**//
if(ldrDurum> 1500){
  ledV = 1;
  digitalWrite(led, ledV);
}
else{
  ledV = 0;
  digitalWrite(led, ledV);
}
//sulama
  if (millis() - previousMillis > interval) {
    previousMillis = millis();
    if(tNem<30 && ledV==1){
    digitalWrite(sumotoru, HIGH);
    digitalWrite(PC13, HIGH);
  //  delay(5000);
  //  digitalWrite(sumotoru, LOW);
  //  digitalWrite(PC13, LOW);
  //  delay(5000);
    }else if(tNem>30  && ledV==1){
      digitalWrite(sumotoru, LOW);
      digitalWrite(PC13, LOW);
    }
  }
}
