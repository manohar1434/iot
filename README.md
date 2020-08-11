#include "MQ135.h"
#include <ESP8266WiFi.h>
String apiKey = "API Key";     //  Enter your Write API key here
const char *ssid =  "WiFI Name";     // Enter your WiFi Name
const char *pass =  "Password"; // Enter your WiFi Password
const char* server = "api.thingspeak.com";
const int sensorPin= 0;
int air_quality;

WiFiClient client;
void setup() 
{
       Serial.begin(115200); 
       delay(10);
       
       Serial.println("Connecting to ");
       Serial.println(ssid);
       WiFi.begin(ssid, pass);
       while (WiFi.status() != WL_CONNECTED) 
     {
            delay(550);
            Serial.print(".");
     }
      Serial.println("");
      Serial.println("WiFi connected");
 }
void loop() 
{
     MQ135 gasSensor = MQ135(A0);
     air_quality = gasSensor.getPPM();
                      if (client.connect(server,80))   
                      {  
                             String postStr = apiKey;
                             postStr +="&field1=";
                             postStr += String(air_quality);
                             postStr += "\r\n\r\n";
 
                             client.print("POST /update HTTP/1.1\n");
                             client.print("Host: api.thingspeak.com\n");
                             client.print("Connection: close\n");
                             client.print("X-THINGSPEAKAPIKEY: "+apiKey+"\n");
                             client.print("Content-Type: application/x-www-form-urlencoded\n");
                             client.print("Content-Length: ");
                             client.print(postStr.length());
                             client.print("\n\n");
                             client.print(postStr);
                             Serial.print("Air Quality: ");
                             Serial.print(air_quality);
                             Serial.println(" PPM.Send to Thingspeak.");
                        }
          client.stop();
          Serial.println("Waiting...");
    delay(10000);
}
