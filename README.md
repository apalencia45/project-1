//************************************
//UNIVERSIDAD DEL VALLE 
//BE3015
//ANDREA PALENCIA 
//19023
//PROYECTO 1
//************************************

//*********************************
//librerías
//*********************************
#include <Arduino.h>
#include "esp_adc_cal.h"
#include "display7seg.h"
#include "config.h"

AdafruitIO_Feed *termometro = io.feed("temp");

//*********************************
//Definición de pines 
//*********************************
#define SensTemp 15
#define Btin  14

//definición pines display 
#define dA 3
#define dB 1
#define dC 23
#define dD 35
#define dE 34
#define dF 36
#define dG 39
#define dP 22

#define display0 32
#define display1 12
#define display2 25

// definición de salidas (leds) 
#define ledRed 4
#define ledYel 17
#define ledGre 18

//definición del servo PWM
#define servo1  26

// definición de variables configuración pwm 
#define pwmServo 0
#define pwmledRed 1
#define pwmledYel 2
#define pwmledGre  3
#define pwmFreq 50  //freq en Hz 
#define pwmFreqled 5000 //frecuencia de los leds
#define res 8

//*********************************
//Configuración de funciones
//*********************************

void leds(); 
void ISRbin(void);
//*********************************
//Variables globales 
//*********************************
//int adctemp =0; 
int botonestado = 0;
float ValTemp = 0.0; 
//float voltaje = 0.0;
double adcfilt = 0; //ema. media móvil exponencial
double alpha = 0.09; 
long lastTime; 
int sampletime =5 ;

//vairables para separar valor de temperatura
int decenas=0;
int unidades=0;
int decimal=0;
int entero=0;

//display 7 segmentos 
uint8_t pinA, pinB, pinC, pinD, pinE, pinF, pinG, pindp;
//*********************************
//ISR
//*********************************
void IRAM_ATTR ISRb(){
  botonestado =1; 
}
//*********************************
//Configuración 
//*********************************
void setup(){
  Serial.begin(115200);

  pinMode(Btin,INPUT_PULLUP);

  pinMode(ledRed,OUTPUT);
  pinMode(ledYel,OUTPUT);
  pinMode(ledGre,OUTPUT);

  lastTime= millis();
  configuraciónpwm();
  attachInterrupt(Btin,ISRb, HIGH); 

  // wait for serial monitor to open
  while(! Serial);

  Serial.print("Connecting to Adafruit IO");

  // connect to io.adafruit.com
  io.connect();

  // wait for a connection
  while(io.status() < AIO_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  // we are connected
  Serial.println();
  Serial.println(io.statusText()); 
}

//*********************************
//Configuración del Loop 
//*********************************
void loop() {
  if (botonestado==1){
    EMPadc();
    botonestado =0;
  Serial.print("adcRaw = ");
  Serial.print(ValTemp);
  Serial.print(" , ");
  Serial.print("TEMPERATURA = ");
  Serial.println(adcfilt);

//Adafruit 
    io.run();

  // save count to the 'counter' feed on Adafruit IO
  Serial.print("sending -> ");
  Serial.println(ValTemp);
  termometro->save(ValTemp);

  //Cálculos para separar números de temperatura
  entero = adcfilt * 10;    
  decenas = entero / 100;          
  entero = entero - (decenas * 100); 
  unidades = entero / 10;          
  entero = entero - (unidades * 10); 
  decimal = entero;       

  }  

}
// conf temperatura 
  
//*********************************
//Funciones 
//*********************************
void EMPadc(void){
  adctemp =analogReadMilliVolts(SensTemp);
  adcfilt= 
}
void configuracionpwm(void){
  void leds(void){
    if (ValTemp < 37.0){
      ledcWrite(pwmServo, 10);
      ledcWrite(pwmledRed, 0); //apagada
      ledcWrite(pwmledYel,0); //apagada
      ledcWrite(pwmledGre,255); //encendida
    }
    if(ValTemp >=37.0){
      ledcWrite(pwmServo, 20);
      ledcWrite(pwmledRed, 0);
      ledcWrite (pwmledYel, 255);
      ledcWrite(pwmledGre,0);
    }
    if (ValTemp >38){
      ledcWrite(pwmServo,30);
      ledcWrite(pwmledRed,255);
      ledcWrite(pwmledYel,0);
      ledcWrite(pwmledGre,0)
    }
  }
  //*********************************
//Funcion  display de 7 segmentos
//*********************************
void configurarDisplay(uint8_t A, uint8_t B, uint8_t C, uint8_t D, uint8_t E, uint8_t F, uint8_t G, uint8_t dp)
{
    pinA = A;
    pinB = B;
    pinC = C;
    pinD = D;
    pinE = E;
    pinF = F;
    pinG = G;
    pindp = dp;

    pinMode(pinA, OUTPUT);
    pinMode(pinB, OUTPUT);
    pinMode(pinC, OUTPUT);
    pinMode(pinD, OUTPUT);
    pinMode(pinE, OUTPUT);
    pinMode(pinF, OUTPUT);
    pinMode(pinG, OUTPUT);
    pinMode(pindp, OUTPUT);

    digitalWrite(pinA, LOW);
    digitalWrite(pinB, LOW);
    digitalWrite(pinC, LOW);
    digitalWrite(pinD, LOW);
    digitalWrite(pinE, LOW);
    digitalWrite(pinF, LOW);
    digitalWrite(pinG, LOW);
    digitalWrite(pindp, LOW);
}
//*********************************
//desplegar el digito en el display de 7 segmentos
//*********************************
void desplegarSeg(uint8_t digito)
{
    switch (digito)
    {
    case 0:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, HIGH);
        digitalWrite(pinF, HIGH);
        digitalWrite(pinG, LOW);
        break;

    case 1:
        digitalWrite(pinA, LOW);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, LOW);
        digitalWrite(pinE, LOW);
        digitalWrite(pinF, LOW);
        digitalWrite(pinG, LOW);
        break;

    case 2:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, LOW);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, HIGH);
        digitalWrite(pinF, LOW);
        digitalWrite(pinG, HIGH);
        break;

    case 3:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, LOW);
        digitalWrite(pinF, LOW);
        digitalWrite(pinG, HIGH);
        break;

    case 4:
        digitalWrite(pinA, LOW);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, LOW);
        digitalWrite(pinE, LOW);
        digitalWrite(pinF, HIGH);
        digitalWrite(pinG, HIGH);
        break;

    case 5:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, LOW);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, LOW);
        digitalWrite(pinF, HIGH); 
        digitalWrite(pinG, HIGH); 
        break;

    case 6:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, LOW);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, HIGH);
        digitalWrite(pinF, HIGH);
        digitalWrite(pinG, HIGH);
        break;

    case 7:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, LOW);
        digitalWrite(pinE, LOW);
        digitalWrite(pinF, LOW);
        digitalWrite(pinG, LOW);
        break;

    case 8:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, HIGH);
        digitalWrite(pinF, HIGH);
        digitalWrite(pinG, HIGH);
        break;

    case 9:
        digitalWrite(pinA, HIGH);
        digitalWrite(pinB, HIGH);
        digitalWrite(pinC, HIGH);
        digitalWrite(pinD, HIGH);
        digitalWrite(pinE, LOW);
        digitalWrite(pinF, HIGH);
        digitalWrite(pinG, HIGH);
        break;

    default:
        break;
    }
}
//*********************
//función punto decimal 
//*********************
void puntodec(boolean punto)
{
    if (punto == 1)
    {
        digitalWrite(pindp, HIGH);
    }

    else
    {
        digitalWrite(pindp, LOW);
    }
}
