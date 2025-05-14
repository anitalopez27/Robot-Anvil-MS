#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

// Crear el objeto PCA9685 en la dirección 0x40
Adafruit_PWMServoDriver pca9685 = Adafruit_PWMServoDriver(0x40);

// Límites del pulso para el servo MG90S
#define SERVO_MIN 102  // Posición 0°
#define SERVO_MAX 530  // Posición 180°
unsigned long lastRecvTime = 0;
char command = 'S';
int tiempo=200;
int tiempo2 = 300;

//////////////////////////////////////////////////////////////////////////////////////////
void moverServo(uint8_t canal, int angulo) {
    int pulso = map(angulo, 0, 180, SERVO_MIN, SERVO_MAX);
    pca9685.setPWM(canal, 0, pulso);
}

/////////////////////////////////////////////////////////////////////////////////////////
void configuracion_inicial_servos_90() 
{
    moverServo(0, 80);   // Servo tobillo izquierdo (Servo 0: 60-110 Hacia afuera- hay que aumentar)
    moverServo(1, 90);   // Servo rodilla 1 izquierda (Servo 1: 25-140 Eleva el pie en 25 y pone el pie hacia atras en 140 )
    moverServo(2, 80);   // Servo rodilla 2 izquierda (Servo 2: 20-120 Eleva la rodilla cuando esta en 20 y la rodilla va para atras en 90)
    moverServo(3, 95);   // Servo cadera izquierda (Servo 3: 10-110 Cuando esta en 10 eleva hacia afuera la pierna)
    
    moverServo(4, 98);  // Servo tobillo derecho (Servo 4: 70-120 Hacia afuera- hay que disminuir)
    moverServo(5, 80);   // Servo rodilla 1 derecha (Servo 5: 35-150 Eleva el pie en 150 y pone el pie hacia atras en 35 )
    moverServo(6, 100);   // Servo rodilla 2 derecha (Servo 6: 80-150 Eleva la rodilla cuando esta en 150 y la rodilla va para atras en 80)
    moverServo(7, 100);   // Servo cadera derecha (Servo 7: 80-170 Cuando esta en 170 eleva hacia afuera la pierna))
    
    moverServo(8, 90);   // Servo que une ambas piernas (Este procurar no tocar debido a que tiene poco rango de movimiento)
    moverServo(9, 100);   // Servo que hace que gire la cadera (Servo 9: 30-150 En 30 se mueve la cadera para la derecha y en 150 mueve la cadera para la izquierda)

    moverServo(10, 0);  // Servo hombro 1 izquierdo (Servo 10: 0-180/el rango de movimiento va a depender de como este ubicado el codo / En 0 mueve el hombro hacia delante - Procurar dejarlo siempre en 0)
    moverServo(11, 85);  // Servo hombor 2 izquierdo (Servo 11: 0-120/el rango de movimiento va a depender de como este ubicado el codo / En 0 mueve el hombro hacia la izquierda o afuera)
    moverServo(12, 160);  // Servo codo izquierdo (Servo 12: 0-180/el rango de movimiento va a depender de como este ubicado el codo / En 180 mueve el codo hacia arriba)
    
    moverServo(13, 180);  // Servo hombro 1 derecho (Servo 13: 0-180/el rango de movimiento va a depender de como este ubicado el codo / En 180 mueve el hombro hacia delante - Procurar dejarlo siempre en 180)
    moverServo(14, 80);  // Servo hombro 2 derecho (Servo 14: 0-120/el rango de movimiento va a depender de como este ubicado el codo / En 180 mueve el hombro hacia la derecha o afuera)
    moverServo(15, 20);  // Servo codo derecho (Servo 15: 0-180/el rango de movimiento va a depender de como este ubicado el codo / En 0 mueve el codo hacia arriba)
    
    delay(100);
}
////////////////////////////////////////////////////////////////////////////////////////////////
void setup() {

    Wire.begin();
    pca9685.begin();
    pca9685.setPWMFreq(50);  // Frecuencia para servos (50Hz)

    //Configuracion de las piernas para que el robot esté erguido
    configuracion_inicial_servos_90();
    Serial.begin(9600);   
}



void loop() {
if(Serial.available() > 0){  
    command = Serial.read(); 
      switch(command){
          
            case 'F':  
                caminar_frente();
            break;
            
            case 'B':  
                caminar_atras();
            break;
            
            case 'L': 
                giro_izquierda();
            break;
            
            case 'R':
                giro_derecha();
            break;
            
            case 'S':  
            break; 
            
            case 'X':  //FR
                golpe_derecho(); 
            break; 
            
            case 'Y':  //BR
                golpe_izquierdo(); 
            break;  
                  
            case ' ': 
            break;
      }
  }   
 
}

//////////////////////////////////////////////////////////////////////////////////////////
void caminar_frente()
{
  brazos_descanso();
  Inicial();
  inclinacion_pizq_120_fuera() ;
  mover_p_der_adelante();
  LevantaD();
  Inicial();
  delay(tiempo);
  inclinacion_pdere_120_fuera()  ;
  mover_p_izq_adelante();
  LevantaI();
  Inicial();
 } 

 void caminar_atras()
{
  brazos_descanso();
  Inicial();
  inclinacion_pizq_120_fuera() ;
  mover_p_der_atras();
  LevantaD();
  Inicial();
  delay(tiempo);
  inclinacion_pdere_120_fuera()  ;
  mover_p_izq_atras();
  LevantaI();
  Inicial();
 } 

 void giro_derecha()
{
  Inicial();
  inclinacion_pizq_120_fuera() ;
  mover_p_der_atras();
  LevantaD();
  Inicial();
 } 

  void giro_izquierda()
{
  Inicial();
  inclinacion_pdere_120_fuera()  ;
  mover_p_izq_atras();
  LevantaI();
  Inicial();
 } 

   void golpe_derecho()
{
  Inicial();
  estirar_brazo_derecho();
  Inicial();
 } 

    void golpe_izquierdo()
{
  Inicial();
  estirar_brazo_izquierdo();
  Inicial();
 } 
////////////////////////////////////////////////////////////////////////////////////////////

void inclinacion_pdere_120_fuera() 
{
    moverServo(5, 65);  // Aqui 
    moverServo(6, 120);
    delay(250);
    moverServo(5, 50);
    moverServo(6, 140);
    moverServo(4, 70);
    delay(tiempo);
}

void inclinacion_pizq_120_fuera() 
{
    moverServo(1, 100); // aqui
    moverServo(2, 40);
    delay(250);
    moverServo(1, 120);
    moverServo(2, 10);
    moverServo(0, 110);
   
    //moverServo(11, 0);
    //moverServo(12, 90); // aqui
    delay(tiempo);
}


////////////////////////////////////////////////////////////////////////////////////////////
void mover_p_izq_adelante()
{
    moverServo(2, 30);
    moverServo(1, 50);
    moverServo(0, 80); 
    delay(tiempo);
}

void mover_p_der_adelante()
{
    moverServo(6, 130);
    moverServo(4, 120);
    moverServo(5, 130); 
    delay(tiempo);
}


////////////////////////////////////////////////////////////////////////////////////////////
void mover_p_izq_atras()
{
    moverServo(1, 140);
    moverServo(2, 80);
    moverServo(4, 98);
    delay(tiempo);
}

void mover_p_der_atras()
{
    moverServo(5, 35);
    moverServo(6, 120);
    moverServo(1, 80);
    delay(tiempo);
}


////////////////////////////////////////////////////////////////////////////////////////////
void Inicial()
{
    moverServo(0, 80);  
    moverServo(1, 90);  
    moverServo(2, 80);  
    moverServo(3, 95);  
    moverServo(4, 98);   
    moverServo(5, 80);  
    moverServo(6, 100);  
    moverServo(7, 100); 
    delay(tiempo);
}


void brazos_descanso()
{
    moverServo(8, 90);  
    moverServo(9, 100);  
    moverServo(10, 0);  
    moverServo(11, 85);  
    moverServo(12, 160);   
    moverServo(13, 180);  
    moverServo(14, 80);  
    moverServo(15, 20); 
    delay(tiempo);
}

void estirar_brazo_derecho()
{
    moverServo(15, 110);
    delay(tiempo2);
    moverServo(12, 70);
    delay(tiempo2);  
    moverServo(15, 20); 
    delay(tiempo2); 
    moverServo(12, 160);
    delay(20);
}

void estirar_brazo_izquierdo()
{
    moverServo(11, 0);
    moverServo(14, 180);
    delay(tiempo2);
    moverServo(15, 110);
    delay(tiempo2);
    moverServo(12, 70);
    delay(tiempo2);  
    moverServo(15, 20); 
    delay(tiempo2); 
    moverServo(12, 160);
    delay(tiempo2);
    moverServo(15, 110);
    delay(tiempo2);
    moverServo(12, 70);
    delay(tiempo2);  
    moverServo(15, 20); 
    delay(tiempo2); 
    moverServo(12, 160);
    delay(tiempo2);
    moverServo(11, 85);
    moverServo(14, 80);
    
}

void LevantaD()
{
    moverServo(0, 80);
    moverServo(2, 90);  
    moverServo(6, 100);
    moverServo(5, 80);  
    delay(tiempo);
}
void LevantaI()
{
    moverServo(6, 100);
    moverServo(2, 80);  
    moverServo(7, 100);
    moverServo(1, 90);  
    moverServo(5, 80);
    moverServo(4, 98);
    delay(20);
}
