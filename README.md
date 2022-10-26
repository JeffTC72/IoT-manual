# Setting up communication between 2 ESP8266 boards with ESP_NOW

## About ESP_NOW

> ESP-NOW is yet another protocol developed by Espressif, which enables multiple devices to communicate with one another without using Wi-Fi. The protocol is similar to the low-power 2.4GHz wireless connectivity that is often deployed in wireless mouses. So, the pairing between devices is needed prior to their communication. After the pairing is done, the connection is secure and peer-to-peer, with no handshake being required.
> ~ [espressif](https://www.espressif.com/en/products/software/esp-now/overview/)

> ESP-NOW is a connectionless communication protocol developed by Espressif that features short packet transmission and can be used with ESP8266 and ESP32 boards.
> ~ [randomnerdtutorial](https://randomnerdtutorials.com/esp-now-esp32-arduino-ide/)

## Requirements
- 2x ESP8266 NodeMCU boards
- Arduino IDE software

## Proof of concept
This manual also functions as a proof of concept for my IoT project. A pair of smart headphones that connect with eachother, allowing user to be able to enjoy music with others in a unique and interactive way. Every user selects 1 song they would like to share and the headset collects these song withing a certain range, then an AI creates a remix. This product solves the problem of boredom, or lack of ideas when it comes to music. It also solves the problem of feeling alone in a crowd, as it allows you to connect with others through music and contribute to a remix of a song. Another fun thing about this is that different places will create different sounding remixes, which teaches user something about modern culture.

![Sketch of concept](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/concept_sketch.jpg)

## Setup
The 2 board we will be using in this manual will be refered to as the 'sender' which will send the data, and the 'reciever' which will -you guessed it- recieve the data. Obviously we start by opening the Adruino IDE software and plugging in 1 of the boards, this board will be your reciever. Go to File > Preferences, where it says 'Additional boards manager URL's' paste this url:
```
http://arduino.esp8266.com/stable/package_esp8266com_index.json
```

![Preferences example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/setup1.jpg)

Then, right of the upload button, go to Select Board > and click the option with the port you're using, there search for 'NodeMCU 1.0' and click OK.
If this doesn't show try to restart the Adruino software.

![Board example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/setup2.jpg)

Now go to Tools > Board > Boards Manager... and search for 'ESP8266', click install

![Board example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/setup3.jpg)

### Getting your boards MAC address

For your sender to know where to send it's data it will need to know the reciever MAC address, this is hardcoded in the board, you cannot give it one yourself. To do this upload the following code, then go to Tools > Serial Monitor.

```
// Complete Instructions to Get and Change ESP MAC Address: https://RandomNerdTutorials.com/get-change-esp32-esp8266-mac-address-arduino/

#include <ESP8266WiFi.h>

void setup(){
  Serial.begin(115200);
  Serial.println();
  Serial.print("ESP8266 Board MAC Address:  ");
  Serial.println(WiFi.macAddress());
}
 
void loop(){

}
```

![MAC example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/setup4.jpg)

Save this MAC address in a notepad or document.

## Sending and Recieving data

Now it's time to plug in 2 boards at once. Open 2 windows of Arduino IDE and select a board to go with it's window, go to Select Board > and click the option with the port you're using, there search for 'NodeMCU 1.0' and click OK.

To the sender board upload the following code:

```
/*
  Rui Santos
  Complete project details at https://RandomNerdTutorials.com/esp-now-esp8266-nodemcu-arduino-ide/
  
  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files.
  
  The above copyright notice and this permission notice shall be included in all
  copies or substantial portions of the Software.
*/

#include <ESP8266WiFi.h>
#include <espnow.h>

// REPLACE WITH RECEIVER MAC Address
uint8_t broadcastAddress[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};

// Structure example to send data
// Must match the receiver structure
typedef struct struct_message {
  char a[32];
  int b;
  float c;
  String d;
  bool e;
} struct_message;

// Create a struct_message called myData
struct_message myData;

unsigned long lastTime = 0;  
unsigned long timerDelay = 2000;  // send readings timer

// Callback when data is sent
void OnDataSent(uint8_t *mac_addr, uint8_t sendStatus) {
  Serial.print("Last Packet Send Status: ");
  if (sendStatus == 0){
    Serial.println("Delivery success");
  }
  else{
    Serial.println("Delivery fail");
  }
}
 
void setup() {
  // Init Serial Monitor
  Serial.begin(115200);
 
  // Set device as a Wi-Fi Station
  WiFi.mode(WIFI_STA);

  // Init ESP-NOW
  if (esp_now_init() != 0) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }

  // Once ESPNow is successfully Init, we will register for Send CB to
  // get the status of Trasnmitted packet
  esp_now_set_self_role(ESP_NOW_ROLE_CONTROLLER);
  esp_now_register_send_cb(OnDataSent);
  
  // Register peer
  esp_now_add_peer(broadcastAddress, ESP_NOW_ROLE_SLAVE, 1, NULL, 0);
}
 
void loop() {
  if ((millis() - lastTime) > timerDelay) {
    // Set values to send
    strcpy(myData.a, "THIS IS A CHAR");
    myData.b = random(1,20);
    myData.c = 1.2;
    myData.d = "Hello";
    myData.e = false;

    // Send message via ESP-NOW
    esp_now_send(broadcastAddress, (uint8_t *) &myData, sizeof(myData));

    lastTime = millis();
  }
}
```

At line 16 change the MAC address placeholder with the MAC address of your reciever board.

To the reciever board upload the following code:

```
/*
  Rui Santos
  Complete project details at https://RandomNerdTutorials.com/esp-now-esp8266-nodemcu-arduino-ide/
  
  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files.
  
  The above copyright notice and this permission notice shall be included in all
  copies or substantial portions of the Software.
*/

#include <ESP8266WiFi.h>
#include <espnow.h>

// Structure example to receive data
// Must match the sender structure
typedef struct struct_message {
    char a[32];
    int b;
    float c;
    String d;
    bool e;
} struct_message;

// Create a struct_message called myData
struct_message myData;

// Callback function that will be executed when data is received
void OnDataRecv(uint8_t * mac, uint8_t *incomingData, uint8_t len) {
  memcpy(&myData, incomingData, sizeof(myData));
  Serial.print("Bytes received: ");
  Serial.println(len);
  Serial.print("Char: ");
  Serial.println(myData.a);
  Serial.print("Int: ");
  Serial.println(myData.b);
  Serial.print("Float: ");
  Serial.println(myData.c);
  Serial.print("String: ");
  Serial.println(myData.d);
  Serial.print("Bool: ");
  Serial.println(myData.e);
  Serial.println();
}
 
void setup() {
  // Initialize Serial Monitor
  Serial.begin(115200);
  
  // Set device as a Wi-Fi Station
  WiFi.mode(WIFI_STA);

  // Init ESP-NOW
  if (esp_now_init() != 0) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  
  // Once ESPNow is successfully Init, we will register for recv CB to
  // get recv packer info
  esp_now_set_self_role(ESP_NOW_ROLE_SLAVE);
  esp_now_register_recv_cb(OnDataRecv);
}

void loop() {
  
}
```

Now upload and open both serial monitors.

The sender board should show this:

![End example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/end1.jpg)

And the reviecer board should show this:

![End example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/end2.jpg)

## Encountered ERRORS

### WiFi error

```
compilation terminated. exit status 1 Compilation error: WiFi.h: No such file or directory
```

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/wifierror.jpg)

This is an error message I unexpectedly encountered while reproducing my process for this manual. The wierd part is that I did this before and succeeded without fail. Here is what I did to fix this:

First I tried to upload the code again, this failed. (obviously)

I found a [video](https://www.programmingelectronics.com/no-such-file-error/) with a step-by-step guide to fix this and found that I, as a matter of fact, did not have the WiFi library. I checked for a library that I tought would have this WiFi library and installed this, this was the Adafruit IO Adruino library, this failed. Then I looked online for a .ZIP of this WiFi library and found it [here](https://www.arduino.cc/reference/en/libraries/wifi/). Going to Sketch > Include Library > Add .ZIP Library.. I added it.

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/wififix1.jpg)
![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/wififix2.jpg)

Now it would surely work right, Wrong! I got a brand new error:

```
Compilation error: 'class WiFiClass' has no member named 'mode'
```

I pasted this message in to Google and found a [stackoverflow post](https://stackoverflow.com/questions/63118195/exit-status-1-class-wificlass-has-no-member-named-softap-in-nodemcu). Here I found that I was using the wrong WiFi library for the ESP8266 board (still, it did work before).

I changed:

```
#include <WiFi.h>
```

To:

```
#include <ESP8266WiFi.h>
```

And was greeted with another red text of death, this one was actually not very bad.

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/wififix3.jpg)

I did the suggested fix and succesfully pulled my MAC address.

### ESP error

```
compilation terminated. exit status 1 Compilation error: esp_now.h: No such file or directory
```

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/esperror.jpg)

When this error showed up I immediately tried to find a .ZIP library for esp_now. I couldn't find this but noticed the libraries I tried to include were named differently in both of my sketches even tho they were supposed to be the same. One had 'WiFi.h' and the other had 'ESP8266WiFi.h', and one had 'esp_now.h' while the other had 'espnow.h'. I changed the to the libraries included in my sender code which was working and got this error:

```
Compilation error: 'class WiFiClass' has no member named 'mode'
```

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/espfix1.jpg)

This I had seen before so I followed the same steps as before, fixing some suggested changes. Then I got this:

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/espfix2.jpg)

To me this is a sign that something bigger is wrong, so I checked the article I was using and noticed that it was meant for the ESP32. *facepalms* this also explains why it did work before without any problems. So I looked for the correct article and changed my code and suprise! It worked... There it is, the lamest error fix ever.
