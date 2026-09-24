#include <BluetoothSerial.h>            // Librería para la conexión Bluetooth

const int relay = 25;                   // Pin del relé 
const int ledPin = 2;                   // LED integrado para verificación visual

BluetoothSerial SerialBT;               // Objeto Bluetooth
String command = "";                    // Variable para almacenar la cadena de texto recibida

void setup() {
  Serial.begin(115200);
  SerialBT.begin("ESP32_Relay");        // Nombre que verás en tu dispositivo Bluetooth
  
  pinMode(relay, OUTPUT);
  pinMode(ledPin, OUTPUT);
  
  // Estado inicial: Relé apagado
  digitalWrite(relay, HIGH);
  digitalWrite(ledPin, LOW);
  
  Serial.println("Dispositivo listo, empareja tu Bluetooth como 'ESP32_Relay'");
}

void loop() {
  // Revisamos si hay datos disponibles en el buffer Bluetooth
  while (SerialBT.available()) {
    char incomingChar = (char)SerialBT.read(); // Leemos caracter por caracter
    
    // Si el caracter es un salto de línea o retorno de carro, procesamos la palabra
    if (incomingChar == '\n' || incomingChar == '\r') {
      command.trim(); // Eliminamos espacios o saltos vacíos adicionales
      
      if (command.length() > 0) {
        // Mostramos en el monitor serial el mensaje exacto que recibió el ESP32
        Serial.print("Mensaje recibido por Bluetooth: ");
        Serial.println(command);
        
        // Comparamos si la palabra recibida es "on" (ignorando mayúsculas/minúsculas)
        if (command.equalsIgnoreCase("on")) {
          digitalWrite(relay, HIGH);       // Activa relé (lógica inversa)
          digitalWrite(ledPin, HIGH);     // Enciende LED integrado
          SerialBT.println("Relé Activado (Current Flowing)");
          Serial.println("Acción: Relé Activado");
        }
        // Comparamos si la palabra recibida es "off"
        else if (command.equalsIgnoreCase("off")) {
          digitalWrite(relay, LOW);      // Desactiva relé
          digitalWrite(ledPin, LOW);      // Apaga LED integrado
          SerialBT.println("Relé Desactivado (Current not Flowing)");
          Serial.println("Acción: Relé Desactivado");
        } else {
          Serial.print("Comando desconocido: ");
          Serial.println(command);
        }
      }
      command = ""; // Limpiamos la variable para el siguiente comando
    } else {
      command += incomingChar; // Vamos acumulando los caracteres
    }
  }
}
