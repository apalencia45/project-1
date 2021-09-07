
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


void loop() {
  // put your main code here, to run repeatedly:
}