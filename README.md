
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
float voltaje = 0.0,
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
}
//*********************************
//Funciones 
//*********************************
void EMPadc(void){
  adctemp =analogReadMilliVolts(SensTemp);
  adcfilt= 
}