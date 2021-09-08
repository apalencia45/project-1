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
#define Btin  13

//definición pines display 
#define dA 19
#define dB 21
#define dC 23
#define dD 32
#define dE 33
#define dF 26
#define dG 25
#define dP 22

#define display0 35
#define display1 39
#define display2 36

// definición de salidas (leds) 
#define ledRed 4
#define ledYel 17
#define ledGre 18

//definición del servo PWM
#define servo1  27

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

//conf temperatura
ValTemp= analogReadMilliVolts(SensTemp)/10.0 ; //corrección
adcfilt=(alpha*ValTemp)+((1-alpha)*adcfilt); //filtro ema

//MOSTRAR NÚMEROS EN DISPLAY
  digitalWrite(display0, HIGH);
  digitalWrite(display1, LOW);
  digitalWrite(display2, LOW);
  desplegarSeg(decenas);  
  puntodec(0);
  lastTime=millis();
  while(millis()  <lastTime + sampletime);

  digitalWrite(display0, LOW);
  digitalWrite(display1, HIGH);
  digitalWrite(display2, LOW);
  desplegarSeg(unidades);  
  puntodec(1);
  lastTime=millis();
  while(millis()  <lastTime + sampletime);

  digitalWrite(display0, LOW);
  digitalWrite(display1, LOW);
  digitalWrite(display2, HIGH);
  desplegarSeg(decimal);  
  puntodec(0);
  lastTime=millis();
  while(millis()  <lastTime + sampletime);

}
}

  
//*********************************
//Funciones 
//*********************************
//FILTRO 
/*void EMPadc(void){
  adctemp =analogReadMilliVolts(SensTemp);
  adcfilt= (alpha *adctemp)+ ((1.0-alpha)* adcfilt);
  ValTemp = (adcfilt/10.0);
  Serial.println(ValTemp);
}*/ 

void configuracionpwm(void){
  ledcSetup(pwmServo, pwmFreq, res); 
  ledcAttachPin(servo1, pwmServo); 
  
  ledcSetup(pwmledRed, pwmFreqled, res); 
  ledcSetup(pwmledGre, pwmFreqled, res); 
  ledcSetup(pwmledYel, pwmFreqled, res); 
  
  ledcAttachPin(ledRed, pwmledRed); 
  ledcAttachPin(ledGre, pwmledGre); 
  ledcAttachPin(ledYel, pwmledYel); 
}

void leds(void){
    if (adcfilt < 12.0){
      ledcWrite(pwmServo, 10);
      ledcWrite(pwmledRed, 0); //apagada
      ledcWrite(pwmledYel,0); //apagada
      ledcWrite(pwmledGre,255); //encendida
    }
    if(adcfilt >=12.5){
      ledcWrite(pwmServo, 20);
      ledcWrite(pwmledRed, 0);
      ledcWrite (pwmledYel, 255);
      ledcWrite(pwmledGre,0);
    }
    if (adcfilt >13){
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

    pinMode(dA, OUTPUT);
    pinMode(dB, OUTPUT);
    pinMode(dC, OUTPUT);
    pinMode(dD, OUTPUT);
    pinMode(dE, OUTPUT);
    pinMode(dF, OUTPUT);
    pinMode(dG, OUTPUT);
    pinMode(dP, OUTPUT);

    digitalWrite(dA, LOW);
    digitalWrite(dB, LOW);
    digitalWrite(dC, LOW);
    digitalWrite(dD, LOW);
    digitalWrite(dE, LOW);
    digitalWrite(dF, LOW);
    digitalWrite(dG, LOW);
    digitalWrite(dP, LOW);
}
//*********************************
//desplegar el digito en el display de 7 segmentos
//*********************************
void desplegarSeg(uint8_t digito)
{
    switch (digito)
    {
    case 0:
        digitalWrite(dA, HIGH);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, HIGH);
        digitalWrite(dE, HIGH);
        digitalWrite(dF, HIGH);
        digitalWrite(dG, LOW);
        break;

    case 1:
        digitalWrite(dA, LOW);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, LOW);
        digitalWrite(dE, LOW);
        digitalWrite(dF, LOW);
        digitalWrite(dG, LOW);
        break;

    case 2:
        digitalWrite(dA, HIGH);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, LOW);
        digitalWrite(dD, HIGH);
        digitalWrite(dE, HIGH);
        digitalWrite(dF, LOW);
        digitalWrite(dG, HIGH);
        break;

    case 3:
        digitalWrite(dA, HIGH);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, HIGH);
        digitalWrite(dE, LOW);
        digitalWrite(dF, LOW);
        digitalWrite(dG, HIGH);
        break;

    case 4:
        digitalWrite(dA, LOW);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, LOW);
        digitalWrite(dE, LOW);
        digitalWrite(dF, HIGH);
        digitalWrite(dG, HIGH);
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
        digitalWrite(dA, HIGH);
        digitalWrite(dB, LOW);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, HIGH);
        digitalWrite(dE, HIGH);
        digitalWrite(dF, HIGH);
        digitalWrite(dG, HIGH);
        break;

    case 7:
        digitalWrite(dA, HIGH);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, LOW);
        digitalWrite(dE, LOW);
        digitalWrite(dF, LOW);
        digitalWrite(dG, LOW);
        break;

    case 8:
        digitalWrite(dA, HIGH);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, HIGH);
        digitalWrite(dE, HIGH);
        digitalWrite(dF, HIGH);
        digitalWrite(dG, HIGH);
        break;

    case 9:
        digitalWrite(dA, HIGH);
        digitalWrite(dB, HIGH);
        digitalWrite(dC, HIGH);
        digitalWrite(dD, HIGH);
        digitalWrite(dE, LOW);
        digitalWrite(dF, HIGH);
        digitalWrite(dG, HIGH);
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
        digitalWrite(dP, HIGH);
    }

    else
    {
        digitalWrite(dP, LOW);
    }
}
