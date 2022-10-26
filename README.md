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
Obviously we start by opening the Adruino IDE software and plugging in 1 of the boards. Go to File > Preferences, where it says 'Additional boards manager URL's' paste this url:
```
http://arduino.esp8266.com/stable/package_esp8266com_index.json
```

![Preferences example](https://github.com/JeffTC72/Iot-manual/blob/main/resources/img/setup1.jpg)

Then, right of the upload button, go to Select Board > and click the option with the port you're using, there search for 'NodeMCU 1.0' and click OK.
If this doesn't show try to restart the Adruino software

