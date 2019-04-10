
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <ArduinoJson.h>
//
const char* ssid = "University of Washington"; // wifi name
const char* pass = ""; // wifi password


typedef struct { // data structure which contains the current location of the ISS 
  String lat;
  String lon;
  String timestamp;
} ISSData;

typedef struct { // data structure which contains sun data (sunrise, sunset, daylength)
  String rise;
  String set;
  String daylength;
} SunData;

ISSData iss; // declaring variables
SunData sun;

void setup() {
  Serial.begin(115200);
  delay(100);
  Serial.print("This board is running: "); // the big 4
  Serial.println(F(__FILE__));
  Serial.print("Compiled: ");
  Serial.println(F(__DATE__ " " __TIME__));
  Serial.print("Connecting to "); Serial.println(ssid);

  WiFi.mode(WIFI_STA); // wifi mode
  WiFi.begin(ssid, pass); // attempts to connect to wifi with given name and password

  while (WiFi.status() != WL_CONNECTED) { // prints dots until connected
    delay(500);
    Serial.print(".");
  }

  Serial.println(); Serial.println("WiFi connected"); Serial.println();
  Serial.print("Your ESP has been assigned the internal IP address ");
  Serial.println(WiFi.localIP()); // local/internal ip address from wifi router

//  String ipAddress = getIP(); // external ip address
  getISS(); // call to populate iss struct
  getSun(iss.lat,iss.lon); // call with iss location parameters to populate sun struct
  Serial.println();
  Serial.println("Timestamp: " + iss.timestamp ); // these lines print the ISS data to the serial stream 
  Serial.println("The International Space Station is currently located at: " + iss.lat + " , " + iss.lon);

  Serial.println("At this latitude and longitude, the sunrise will be at " + sun.rise + ", and the sunset will be at " + sun.set); // these lines print the sun data to the serial stream
  Serial.println("The day will be this long: " + sun.daylength);
}

void loop() {
}


void getSun(String lat, String lon) {
  HTTPClient theClient; // initialize browser
  Serial.println("Making HTTP request");
  Serial.println("http://api.sunrise-sunset.org/json?lat=" + lat + "&lng=" + lon);// for debugging purposes, print the url

  theClient.begin("http://api.sunrise-sunset.org/json?lat=" + lat + "&lng=" + lon);// attempt to make connection
  int httpCode = theClient.GET(); // get response code
  
  if (httpCode > 0) {
    if (httpCode == 200) { // 200 means its working
      Serial.println("Received HTTP payload.");
      DynamicJsonBuffer jsonBuffer; // json buffer will parse payload
      String payload = theClient.getString(); // gets the String payload from the website, which is in json format

      Serial.println("Parsing...");
      JsonObject& root = jsonBuffer.parse(payload);// json object will now contain key value pair data  
      String stringResults = root["results"].as<String>();
      JsonObject& results = jsonBuffer.parse(stringResults); // parse again since the data from the website is a "nested" json 
      // Test if parsing succeeds.
      if (!root.success()) { // if parse works
        Serial.println("parseObject() failed");
        Serial.println(payload);
        return;
      }

      //Using .dot syntax, we refer to the variable "sun" 
      sun.rise = results["sunrise"].as<String>();            //we cast the values as Strings b/c
      sun.set = results["sunset"].as<String>();  //the 'slots' in SunData are Strings
      sun.daylength = results["day_length"].as<String>(); // all of these lines populate the SunData struct object with sun data
      
    } else {
      Serial.println(httpCode);
      Serial.println("Something went wrong with connecting to the endpoint.");
    }
  } else {
    Serial.println(httpCode);
  }
}
void getISS() {
  HTTPClient theClient; // initialize browser
  Serial.println("Making HTTP request");
  Serial.println("http://api.open-notify.org/iss-now.json");// for debugging purposes, print the url

  theClient.begin("http://api.open-notify.org/iss-now.json");// attempt to make connection
  int httpCode = theClient.GET(); // get response code

  if (httpCode > 0) {
    if (httpCode == 200) { // 200 means its working
      Serial.println("Received HTTP payload.");
      DynamicJsonBuffer jsonBuffer; // json buffer will parse payload
      String payload = theClient.getString(); // gets the String payload from the website, which is in json format

      Serial.println("Parsing...");
      JsonObject& root = jsonBuffer.parse(payload);// json object will now contain key value pair data
      String ISSloc = root["iss_position"].as<String>();
      JsonObject& loc = jsonBuffer.parse(ISSloc);// parse again since the data from the website is a "nested" json 
      
      // Test if parsing succeeds.
      if (!root.success()) { // if parse works
        Serial.println("parseObject() failed");
        Serial.println(payload);
        return;
      }
      //Using .dot syntax, we refer to the variable "iss" 
 
      iss.timestamp = root["timestamp"].as<String>();            //we cast the values as Strings b/c
      iss.lat = loc["latitude"].as<String>();  //the 'slots' in ISSData are Strings
      iss.lon = loc["longitude"].as<String>(); // all of these lines populate the ISSData struct object with ISS data
     
    } else {
      Serial.println(httpCode);
      Serial.println("Something went wrong with connecting to the endpoint.");
    }
  } else {
    Serial.println(httpCode);
  }
}
