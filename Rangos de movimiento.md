#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

Adafruit_PWMServoDriver pca9685 = Adafruit_PWMServoDriver(0x40);

// Ajusta estos valores si ves que tu servo se forza en los extremos
#define SERVO_MIN 102  // Pulso para 0°
#define SERVO_MAX 530  // Pulso para 180°

void setup() {
  Serial.begin(9600);
  Wire.begin();
  pca9685.begin();
  pca9685.setPWMFreq(50);  // Frecuencia para servos

  Serial.println("Envia: canal angulo");
  Serial.println("Ejemplo: 3 90   (mueve el servo del canal 3 a 90 grados)");
}

void loop() {
  if (Serial.available()) {
    int canal = Serial.parseInt();
    int angulo = Serial.parseInt();

    if (canal >= 0 && canal < 16 && angulo >= 0 && angulo <= 180) {
      int pulso = map(angulo, 0, 180, SERVO_MIN, SERVO_MAX);
      pca9685.setPWM(canal, 0, pulso);

      Serial.print("Servo en canal ");
      Serial.print(canal);
      Serial.print(" movido a ");
      Serial.print(angulo);
      Serial.println(" grados.");
    } else {
      Serial.println("Entrada invalida. Usa: canal(0-15) angulo(0-180)");
    }

    // Limpia el buffer por si entra más texto de golpe
    while (Serial.available()) Serial.read();
  }
}
