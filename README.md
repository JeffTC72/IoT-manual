# Setting up communication between 2 ESP8266 boards with EPS_NOW

## About ESP_NOW

> ESP-NOW is yet another protocol developed by Espressif, which enables multiple devices to communicate with one another without using Wi-Fi. The protocol is similar to the low-power 2.4GHz wireless connectivity that is often deployed in wireless mouses. So, the pairing between devices is needed prior to their communication. After the pairing is done, the connection is secure and peer-to-peer, with no handshake being required.
> ~ [espressif](https://www.espressif.com/en/products/software/esp-now/overview/)

> ESP-NOW is a connectionless communication protocol developed by Espressif that features short packet transmission. This protocol enables multiple devices to talk to each other in an easy way.
> ~ [randomnerdtutorial](https://randomnerdtutorials.com/esp-now-esp32-arduino-ide/)

## Requirements
- 2x EPS8266 NodeMCU boards
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
#include <ESP8266WiFi.h>
 
void setup(){
  Serial.begin(115200);
  WiFi.mode(WIFI_AP_STA);
  Serial.println(WiFi.macAddress());
}
 
void loop(){

}
```








## Encountered ERRORS

```
compilation terminated. exit status 1 Compilation error: WiFi.h: No such file or directory
```

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/wifierror.jpg)

This is an error message I unexpectedly encountered while reproducing my process for this manual. The wierd part is that I did this before and succeeded without fail. Here is what I did to fix this:

First I tried to upload the code again, this failed. (obviously)

I found a [video](https://www.programmingelectronics.com/no-such-file-error/) with a step-by-step guide to fix this and found that I, as a matter of fact, did not have the WiFi library. I checked for a library that I tought would have this WiFi library and installed this, this was the Adafruit IO Adruino library, this failed. Then I looked online for a .ZIP of this WiFi library and found it [here](https://www.arduino.cc/reference/en/libraries/wifi/). Going to Sketch > Include Library > Add .ZIP Library.. I added it.

![Error message](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/wififix1.jpg)
