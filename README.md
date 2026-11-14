
#include <ESP32Servo.h>

// Definición de pines
const int pinPIR = 2;
const int pinServo = 13;
const int pinLED = 5;

// Variables del comedero
Servo dispensador;

int anguloDispensa = 140;
int anguloCerrado = 40;

bool dispensado = false;

void setup() {
  Serial.begin(115200);

  pinMode(pinPIR, INPUT);
  pinMode(pinLED, OUTPUT);

  dispensador.attach(pinServo);
  dispensador.write(anguloCerrado);

  digitalWrite(pinLED, LOW);

  Serial.println("Comedero Inteligente: Modo EN ESPERA...");
}

void loop() {

  // Detectar si hay un gato
  int hayGato = digitalRead(pinPIR);

  if (hayGato == HIGH) {

    if (dispensado == false) {

      Serial.println(">>> ¡Gato detectado! Dispensando alimento...");

      // Abrir el dispensador
      dispensador.write(anguloDispensa);

      // Encender LED
      digitalWrite(pinLED, HIGH);

      delay(1500);

      // Cerrar el dispensador
      dispensador.write(anguloCerrado);

      // Apagar LED
      digitalWrite(pinLED, LOW);

      // Registrar que ya se sirvió comida
      dispensado = true;

      Serial.println("Dispensacion completada. Servo cerrado.");

      // Evitar nuevas activaciones inmediatas
      delay(10000);
    }

  } else {

    // Si el gato se fue, permitir otra dispensación
    if (dispensado == true) {

      dispensado = false;

      Serial.println("Sistema reiniciado. Listo para la proxima comida.");
    }

    delay(500);
  }
}
