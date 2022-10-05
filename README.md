# IoT
Para fins educacionais 
Codigo da SA2 de IoT - integração do ESP com a rede wifi usando o ThingSpeak

#include <DHTesp.h>
#include "WiFi.h"
#include "HTTPClient.h"

DHTesp dhtSensor;
HTTPClient http;

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const String url = "https://api.thingspeak.com/update?api_key=JBDKIME8VSBWEAMQ&";

void setup() {
  
  Serial.begin(115200);
  Serial.println("Iniciando setup");
  WiFi.begin(ssid,password);

  while (WiFi.status() != WL_CONNECTED){
    delay(500);
    Serial.println("Connecting to WIFI...");
  }

  dhtSensor.setup(13,DHTesp::DHT22);
  pinMode(14,OUTPUT);
  pinMode(22,OUTPUT);

  Serial.println("Finalizando setup");
}

void loop() {
  //capturando a temperatura e umidade
  float temperatura = dhtSensor.getTemperature();
  float umidade = dhtSensor.getHumidity();

  //Imprimindo os dados capiturados
  Serial.println("Temperatura :" + String(temperatura) + "ºC"); 
  Serial.println("Umidade :" + String(umidade) + "%"); 
  Serial.println("_____________________________________"); 
  
  String path = url + "field1=" + String(temperatura) + "&field2=" + String(umidade);
  
  http.begin(path);
  int httpCode = http.GET();
  String payload = http.getString();
  Serial.println("httpcode :" + String(httpCode));
  Serial.println("Payload:" + payload);

  delay(1000);
  
  digitalWrite(14, HIGH);
  digitalWrite(22, HIGH);
  delay(1000);

  digitalWrite(14, LOW);
  digitalWrite(22, LOW);
}
