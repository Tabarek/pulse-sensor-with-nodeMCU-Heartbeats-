 #include <ESP8266WiFi.h>
 const char* ssid     = "SSID";
 const char* password = "PASSWORD";
 double alpha=0.75;
 static double oldValue=0;
 int period=20;
 double refresh=0.0;
 const char* host = "URL IP";

void setup() {
  Serial.begin(115200);
  delay(500);


 // We start by connecting to a WiFi network

  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  
  /* Explicitly set the ESP8266 to be a WiFi-client, otherwise, it by default,
     would try to act as both a client and an access-point and could cause
     network-issues with your other WiFi-devices on your WiFi-network. */
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");  
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}


void loop() {
  delay(5000);
   Serial.print("connecting to ");
  Serial.println(host);
  
  // Use WiFiClient class to create TCP connections
  WiFiClient client;
  const int httpPort = 80;
  if (!client.connect(host, httpPort)) {
    Serial.println("connection failed");
    return;
  }
  
  // We now create a URI for the request
  String url = "URL";
  
int beat=analogRead(17);
   double pulse=alpha*oldValue+(0-alpha)*beat;
   refresh=pulse-oldValue;
   Serial.print(" value:  ");
   Serial.println(beat/7);
   oldValue=pulse;
  double oldrefresh=refresh;
   delay(period*7);
   
   url += "&value=";
  url += beat/7;
 Serial.println(" ..... ");
 
  Serial.print("Requesting URL:");
  Serial.println(url);
  
  // This will send the request to the server
  client.print(String("GET ") + url + " HTTP/1.1\r\n" +
               "Host: " + host + "\r\n" + 
               "Connection: close\r\n\r\n");
  unsigned long timeout = millis();
  while (client.available() == 0) {
    if (millis() - timeout > 5000) {
      Serial.println(">>> Client Timeout !");
      client.stop();
      return;
    }
  }
  
  // Read all the lines of the reply from server and print them to Serial
  while(client.available()){
    String line = client.readStringUntil('\r');
    Serial.print(line);
  }

  
  
  
  
  delay(500);
  Serial.println("Client disonnected");
  Serial.println("");
  
  Serial.println();
  Serial.println("closing connection");
}
  
  



 
