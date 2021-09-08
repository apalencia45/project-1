//Adafruit.IO
#define IO_USERNAME  "apalencia"
#define IO_KEY       "aio_kqFQ876a69JM01EsJ3DbNPuDoz7O"


//Parte de WIFI
#define WIFI_SSID "andrea2" 
#define WIFI_PASS "febrero2"

AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);
AdafruitIO_Feed *termometro = io.feed("Proyecto 1");



//*********************************
//librerías
//*********************************
#include <Arduino.h>
#include "esp_adc_cal.h"

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
void setup(){
  Serial.begin(115200);

  pinMode(Btin,INPUT_PULLUP);

  pinMode(ledRed,OUTPUT);
  pinMode(ledYel,OUTPUT);
  pinMode(ledGre,OUTPUT);

  lastTime= millis();
  configuraciónpwm();
  attachInterrupt(Btin,ISRb, HIGH); 
}
//*********************************
//Configuración del PWM del servo 
//*********************************
void configuracionSLPWM(void)
{
  //  Configurar el módulo PWMservo 
  ledcSetup(pwmServo, pwmFreq, res); //Unimos las variables determinadas arriba

  
  ledcAttachPin(servo1, pwmServo); //selección del pin 

// configurar modulo pwm leds 
  ledcSetup(pwmledRed, pwmFreqled, res); //Unimos las variables determinadas arriba
  ledcSetup(pwmledYel, pwmFreqled, res); 
  ledcSetup(pwmledGre, pwmFreqled, res); 

 
  ledcAttachPin(ledRed, pwmledRed); //seleccion del pin 
  ledcAttachPin(ledGre, pwmledGre; 
  ledcAttachPin(ledYel, pwmledYel); 
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
