//Adafruit.IO
#define IO_USERNAME  "EsBatz"
#define IO_KEY       "aio_lWcG42G3XvoZmC8IRW9tatg3uYtU"

//Parte de WIFI
#define WIFI_SSID "Batz" 
#define WIFI_PASS "141994Jo"

AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);
AdafruitIO_Feed *termometro = io.feed("D2_Proyecto1");



//*********************************
//librerías
//*********************************
#include <Arduino.h>
#include "esp_adc_cal.h"

//*********************************
//Definición de pines 
//*********************************
#define SensTemp 35
#define Btin  36

//definición pines display 
#define dA
#define dB
#define dC
#define dD
#define dE
#define dF 16
#define dG 17
#define dP

#define display0 15 
#define display1 2
#define display2 4

// definición de salidas (leds) 
#define ledRed 12
#define ledYel 27
#define ledGre 25

//definición del servo PWM
#define servo1  23

// definición de variables configuración pwm 
#define pwmServo 0
#define pwmledRed 1
#define pwmledYel 2
#define pwmledGre  3
#define pwmFreq 50  //freq en Hz 
#define res 8

//*********************************
//Configuración de funciones
//*********************************
void configuraciónpwm();
void leds(); 

//*********************************
//Variables globales 
//*********************************
int adctemp =0; 
int botonestado = 0;
float ValTemp = 0.0; 
float voltaje = 0.0;

// adaffruit
int count = 0;
long LastTime; 
int sampleTime = 3000;
//*********************************
//ISR
//*********************************
void IRAM_ATTR ISRb(){
  botonestado =1; 
}
//*********************************
//Configuración 
//*********************************
void setuo(){
  Serial.begin(115200);

  pinMode(Btin,INPUT_PULLUP);

  pinMode(ledRed,OUTPUT);
  pinMode(ledYel,OUTPUT);
  pinMode(ledGre,OUTPUT);

  configuraciónpwm();
  attachInterrupt(Btin,ISRb, HIGH); // ?? 
}
//*********************************
//Configuración del Loop 
//*********************************
void loop() {
  if (botonestado==1){
    EMPadc();
    botonestado =0;
  }  







  //Adafruit
  while(! Serial);

  Serial.print("Connecting to Adafruit IO");
  io.connect();

  while(io.status() < AIO_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  Serial.println();
  Serial.println(io.statusText());
}
//*********************************
//Funciones 
//*********************************
void EMPadc(void){
  adctemp =analogReadMilliVolts(SensTemp);
  adcfilt= 
}