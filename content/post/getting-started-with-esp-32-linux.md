---
title: "Getting Started With ESP 32 Development"
date: 2022-08-04T00:42:04-07:00
lastmod: 2022-08-04T00:42:04-07:00
draft: false
keywords: ["esp32", "micro controller", "arduino", "linux", "programming", "c++", "python", "wifi", "bluetooth"]
description: "Getting Started With ESP 32 Development in Linux using Arduino"
tags: ["esp32", "micro controller", "arduino", "linux", "programming", "c++", "python", "wifi", "bluetooth"]
categories: ["esp32"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---


ESP 32 is a series of low-cost, low-power system on chip micro controllers with integrated Wi-FI and Bluetooth. SoC or system on chip is an integrated circuit that integrates all or most of the component of a computer. Example the CPU, RAM, IO interface, GPU, etc. This form is becoming more and more popular thanks to growing smartphone and computing industry. Apple M1 chip is a good example on the direction in which SoC's are going.

The ESP 32 is really low cost it cost like 350 in India, and less than 10$ in the US. Now when you buy this you don't buy the chip alone, but a chip on a development board. There are several articles ([https://makeradvisor.com/esp32-development-boards-review-comparison/](https://makeradvisor.com/esp32-development-boards-review-comparison/)) on that. The board that I bought from amazon was this [https://www.amazon.com/dp/B0718T232Z?psc=1&ref=ppx\_yo2ov\_dt\_b\_product\_details](https://www.amazon.com/dp/B0718T232Z?psc=1&ref=ppx\_yo2ov\_dt\_b\_product\_details), i also bought one with OLED display as well [https://www.amazon.com/dp/B07DKD79Y9?psc=1&ref=ppx\_yo2ov\_dt\_b\_product\_details](https://www.amazon.com/dp/B07DKD79Y9?psc=1&ref=ppx\_yo2ov\_dt\_b\_product\_details).

In this article I will be talking about how to get started. We will basically will be setting up Aurdino IDE in linux, followed by flashing few sample programs like:

<!--more-->

Note: Arduino is another chip like esp32 (costlier but similar). ESP32 is can be made working on the same IDE. ESP32 can also be programmed using circuit python (which uses micro python), mruby, etc. But since there are a lot of popular code examples available for Arduino IDE esp32, I personally recommend getting started and working with Arduino. 

The language used in Arduino is C++, and the C++ program is written to esp32 board using python (esptool.py).  

To setup Arduino:

1\. Download the linux installation files from https://www.arduino.cc/en/software
2\. Extract the contents of the file downloaded ( a .tar.xz) to the home folder
3\. Open terminal
4\. `cd ~/&lt;folder_name&gt;``
5. `sh install.sh`

This would install Arduino IDE in your computer, after which connect the esp32 device to your computer USB port.
Now do `ls -l /dev/ttyUSB*`. You will see the USB port number to which you have connected the board.

The output would look like this:
```sh
crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyUSB0
```

You need to add your logged in user to the group dialout for the IDE to be able to access the board. So run the following command.

```sh
sudo usermod -a -G dialout <username>
```

You will have to log out and log back in for the settings to take hold, you could also quickly restart your computer if you wish.

Once this is done, you can open up your `Arduino IDE`

![Arduino IDE](/images/esp32_linux/Aurdino_ide.png)

Next step is to add the board details to the Arduino IDE.

To do that, go to:

1. File > Preference
2. In the section additional boards manager URL text box, paste the following urls:
	```sh
	https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json, http://arduino.esp8266.com/stable/package_esp8266com_index.json
	```

Now this will load a log of ESP32 and ESP8266 board details and chances are this would have your board as well.

![Different Board](/images/esp32_linux/different_boards.png)

I selected the ESP32 Dev Module in the list.

Now, since we have our board selected, we can run some examples. When we added the preference URL, along with getting the board details it also got some examples. A good code to get started with is the wifi scan. To load the program go to `file > examples > Under Heading Examples for ESP32 Dev Modules > WiFi > Wifi Scan`

A window will open up with the code example.

Click the right arrow and it will start writing the code to the board.

![compile_and_write_to_board.png](/images/esp32_linux/compile_and_write_to_board.png)


Once you get started you might/will (depending on the board) see this following error.

![chip_download_mode_error.png](/images/esp32_linux/chip_download_mode_error.png)


Failed to connected to ESP32: Wrong boot mode detected (0x13)! The chip needs to be in download mode.

What this means is that you need to enable the download mode on the chip, and to do that you need to click and hold the boot button on the board while the program tries to upload the program. The below image is where the button is on my board, it would most probably be the same for you as well.

![boot_button.png](/images/esp32_linux/boot_button.png)

Now when you click the right button, you will see that it is overwriting what ever existing program it might be having.

To see the output of your work, click the magnifying glass on the top right corner of your Arduino board.
![serial_monitor.png](/images/esp32_linux/serial_monitor.png)

That Serial monitor, click the other button on your board and your program will now be running and it will slowly start to display all the wifi networks it can see.

There are other examples available as well feel free to play around with your board, and build something amazing.

PS: my board says ESP32-WROOM, its not that much different from ESP32 it just has more flash memory.


Code to control the onboard LED Light:

```c++
#define ONBOARD_LED  2

void setup() {
  pinMode(ONBOARD_LED,OUTPUT);
}

void loop() {
  delay(1000);
  digitalWrite(ONBOARD_LED,HIGH);
  delay(100);
  digitalWrite(ONBOARD_LED,LOW);
}
```

Code to control the onboard LED light using a HTTP server

```c++

#include <WiFi.h>
#include <WiFiClient.h>
#include <WebServer.h>
#include <ESPmDNS.h>

const char *ssid = "DragonStone";
const char *password = "passwordillak3t0";
const int onboardLed = 2;

WebServer server(80);


void handleRoot() {

  String index = "<html>\
  <head>\
    <meta http-equiv='refresh' content='5'/>\
    <title>ESP32 LED Controller</title>\
    <style>\
      body { background-color: #cccccc; font-family: Arial, Helvetica, Sans-Serif; Color: #000088; }\
    </style>\
  </head>\
  <body>\
    <h1>Hello from ESP32!</h1>\
    <p><a href='/on'>ON</a><br><br><br><a href='/off'>off</a>\
  </body>\
</html>";
  server.send(200, "text/html", index);
}

void handleNotFound() {
  String message = "File Not Found\n\n";
  message += "URI: ";
  message += server.uri();
  message += "\nMethod: ";
  message += (server.method() == HTTP_GET) ? "GET" : "POST";
  message += "\nArguments: ";
  message += server.args();
  message += "\n";

  for (uint8_t i = 0; i < server.args(); i++) {
    message += " " + server.argName(i) + ": " + server.arg(i) + "\n";
  }

  server.send(404, "text/plain", message);
}

void setup(void) {
  pinMode(onboardLed, OUTPUT);
  digitalWrite(onboardLed, 0);
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.println("");
  pinMode(onboardLed,OUTPUT);

  // Wait for connection
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.print("Connected to ");
  Serial.println(ssid);
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  if (MDNS.begin("esp32")) {
    Serial.println("MDNS responder started");
  }

  server.on("/", handleRoot);
  server.on("/on", []() {
    digitalWrite(onboardLed,HIGH);
    server.send(200, "text/html", "led is ON. <br><a href='/'>Go Back</a>");
  });
  server.on("/off", []() {
    digitalWrite(onboardLed,LOW);
    server.send(200, "text/html", "led is off. <br><a href='/'>Go Back</a>");
  });
  server.onNotFound(handleNotFound);
  server.begin();
  Serial.println("HTTP server started");
}

void loop(void) {
  server.handleClient();
  delay(2);//allow the cpu to switch to other tasks
}

```

Happy Codding
