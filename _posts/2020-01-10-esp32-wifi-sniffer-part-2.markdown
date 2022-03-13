---
layout: post
title:  "ESP32 WiFi Sniffer[Part 2]"
date:   2020-01-10 08:50:22 -0500
categories: [Programming, Security]
---
# Igredients
* [Everything Setup From Part One](https://metalfacesec.github.io/infosec/security/esp32/2020/01/09/esp32-wifi-sniffer-part-1.html)
* [Jumper Wires](https://www.amazon.com/Haitronic-Multicolored-Breadboard-Arduino-Raspberry/dp/B01LZF1ZSZ)
* [2x 10k Ohm Resistors](https://www.amazon.com/Projects-100EP51210K0-10k-Resistors-Pack/dp/B0185FIOTA)
* [2x Momentary Push Button Switches](https://www.amazon.com/6x6x6mm-Momentary-Push-Button-Switch/dp/B01GN79QF8)

# Introduction
In [part one](https://metalfacesec.github.io/infosec/security/esp32/2020/01/09/esp32-wifi-sniffer-part-1.html) we setup our ESP32 to make a big list of every MAC address it finds and display it in a paginated format on an OLED screen. Along the way we tested for promiscuous mode on the WiFi adapter and went over scanning for the i2c address of your device. If you haven’t read [part one](https://metalfacesec.github.io/infosec/security/esp32/2020/01/09/esp32-wifi-sniffer-part-1.html), I suggest you check it out [here](https://metalfacesec.github.io/infosec/security/esp32/2020/01/09/esp32-wifi-sniffer-part-1.html). In part two we are going to add some buttons to control the page being displayed, giving us better control over the pagination of the displayed data.

# Hooking Up Buttons
Let's get this article started by hooking up two buttons that we will then use to change the function of our ESP32 and also to do things like change page on our WiFi sniffer. Hook the pins of your buttons up to your breadboard in an open space as shown in the left image below. On the pins on the left side of the button we want to connect a jumper cable from two of our GPIO pins to the left side of each switch.  Make sure to put this in the circuit before the resistor as seen in the middle image below. Below that pin in the same row we want to connect one side of our 10k Ohm resistor and the other side of the resistor gets connected to the ground(also shown in the middle image below). On the pins on the right side of each switch, we want to hook up our 3.3 volt power source.  You can see what the complete circuit should look like in the rightmost image below. If you notice that your resistors are not fitting as well as mine are in the picture, it’s because I snipped about half the length of each terminal off with some wire cutters. This step is optional but I find it makes the resistor easier to work with.

<br />
<center><img src="\assets\esp-sniffer-pt2\1.jpg" width="200" hspace="10" /><img src="\assets\esp-sniffer-pt2\2.jpg" width="200" hspace="10" /><img src="\assets\esp-sniffer-pt2\3.jpg" width="200" hspace="10" /></center>
<br />

# Testing Our Buttons
Before we start hacking away at our code from [part one](INSERT LINK), lets use a really quick small script to make sure our buttons are working. In the code snippet below we first define the GPIO pins we connected our buttons to on our ESP32.  For me this was pin 15 and pin 2. If you follow the pictures above, yours will be the same.  Next, in the ‘setup’ function we set our baud rate. In our ‘loop’ function is where our test is happening. This block of code reads the value from each button, casts it to a string and prints it on the serial monitor. This value will be 0 if the button is not being pressed and will read 1 when the button is pressed.  You can now upload this code to your ESP32 and open the serial monitor(Ctrl+Shift+M). As you press the buttons you should see the output change from 0 to 1 in the string logged out.

```cpp
#define BUTTON_LEFT_GPIO 15
#define BUTTON_RIGHT_GPIO 2

void setup() {
    Serial.begin(115200);
}

void loop() {
    String leftButtonValue =  digitalRead(BUTTON_LEFT_GPIO);
    String rightButtonValue =  digitalRead(BUTTON_RIGHT_GPIO);
    
    Serial.println(leftButtonValue + " - " + rightButtonValue);
    delay(500);
}
```

# Change Pages On Button Press
Now that we have confirmed that are buttons are both working we are going to want to open back up our [code from part one](https://metalfacesec.github.io/infosec/security/esp32/2020/01/09/esp32-wifi-sniffer-part-1.html). We are going to add a few functions that can be found below to this code.  We are also going to make some changes to our ‘loop’ function but first, lets go over the new functions we are creating. The top two define lines are setting the GPIO pins we used for our two buttons. Underneath that we have three new functions. The top function ‘wasButtonReleased’ looks at the last reading for the right or left button and tells us once it goes from being pressed to being released. Note that this function has a current limitation of only working with two buttons. The next two functions ‘hasMoreMACPages’ and ‘hasLowerMACPages’ tell us if we have pages to scroll up or down to.

```cpp
#define BUTTON_LEFT_GPIO 15
#define BUTTON_RIGHT_GPIO 2

boolean wasButtonReleased(bool isRight, int reading) {
    if (lastRightButtonRead == 1 && reading == 0 && isRight) {
        return true;
    } else if (lastLeftButtonRead == 1 && reading == 0 && !isRight) {
        return true;
    }
    return false;
}

boolean hasMoreMACPages() {
   return currentPage < getMaxPages();
}

boolean hasLowerMACPages() {
   return currentPage > 1;
}
```

Now let's take a look at how we implement these new functions.  In the 'loop' function we are going to remove the lines for looping through the pages that we created in [part one](https://metalfacesec.github.io/infosec/security/esp32/2020/01/09/esp32-wifi-sniffer-part-1.html) and add the lines of code shown below. The top chunk of code here used the GPIO pin definitions we defined above and reads the value from each of those two pins. Once it has the current value it passed it to our ‘wasButtonReleased’ function for each button.  It also uses our other two new functions to check if it has pages to scroll up or down to. This stops the user from scrolling up or down to pages that don’t exist. The last chink of code sets the last value for each button to the current value allowing the next loop to keep doing the same check we just performed with the correct value for the last run.

```cpp
int leftButtonRead = digitalRead(BUTTON_LEFT_GPIO);
int rightButtonRead = digitalRead(BUTTON_RIGHT_GPIO);

if (wasButtonReleased(true, rightButtonRead) && hasMoreMACPages()) {
    currentPage++;
} else if (wasButtonReleased(false, leftButtonRead) && hasLowerMACPages()) {
    currentPage--;
}

lastRightButtonRead = rightButtonRead;
lastLeftButtonRead = leftButtonRead;
```

Your full code should now look like the code below.

```cpp
#include "esp_wifi.h"
#include <vector>
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

using namespace std;

#define MAX_CHANNELS 11
#define MAX_MACS_ON_SCREEN 5
#define OLED_RESET -1
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define BUTTON_LEFT_GPIO 15
#define BUTTON_RIGHT_GPIO 2


Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

int lastLeftButtonRead = 0;
int lastRightButtonRead = 0;
int curChannel = 1;
int currentPage = 1;
long lastPageChange = 0;
vector<String> macArray;

const wifi_promiscuous_filter_t filt = {
    .filter_mask=WIFI_PROMIS_FILTER_MASK_MGMT|WIFI_PROMIS_FILTER_MASK_DATA
};

typedef struct {
    uint8_t mac[6];
} __attribute__((packed)) MacAddr;

typedef struct {
  int16_t fctl;
  int16_t duration;
  MacAddr da;
  MacAddr sa;
  MacAddr bssid;
  int16_t seqctl;
  unsigned char payload[];
} __attribute__((packed)) WifiMgmtHdr;

void sniffer(void* buf, wifi_promiscuous_pkt_type_t type) {
    wifi_promiscuous_pkt_t *p = (wifi_promiscuous_pkt_t*)buf;
    WifiMgmtHdr *wh = (WifiMgmtHdr*)p->payload;

    MacAddr mac_add = (MacAddr)wh->sa;
    String macAttack;
    for (int i = 0; i < sizeof(mac_add.mac); i++) {
        String macDigit = String(mac_add.mac[i], HEX);
        if (macDigit.length() == 1) {
            macDigit = "0" + macDigit;
        }
        
        macAttack += macDigit;
        if (i != sizeof(mac_add.mac) - 1) {
          macAttack += ":";
        }
    }

    macAttack.toUpperCase();

    // Prevent duplicates
    for (int i = 0; i < macArray.size(); i++) {
        if (macAttack == macArray[i]) {
            return;
        }
    }

    macArray.push_back(macAttack);   
}

int getMaxPages() {
    int maxPages = macArray.size() / MAX_MACS_ON_SCREEN;
    if (macArray.size() % MAX_MACS_ON_SCREEN > 0) {
        maxPages++;
    }
    return maxPages;
}

void setWifiPromiscuousMode() {
    wifi_init_config_t cfg = WIFI_INIT_CONFIG_DEFAULT();
    esp_wifi_init(&cfg);
    esp_wifi_set_storage(WIFI_STORAGE_RAM);
    esp_wifi_set_mode(WIFI_MODE_NULL);
    esp_wifi_start();
    esp_wifi_set_promiscuous(true);
    esp_wifi_set_promiscuous_filter(&filt);
    esp_wifi_set_promiscuous_rx_cb(&sniffer);
    esp_wifi_set_channel(curChannel, WIFI_SECOND_CHAN_NONE);
}

void setupDisplay() {
    if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
        Serial.println(F("SSD1306 allocation failed"));
        for(;;);
    }
  
    display.setTextSize(1);
    display.setTextColor(WHITE);
}

void setup() {
    Serial.begin(115200);
  
    setWifiPromiscuousMode();
    setupDisplay();
    displayFoundMac();
}

void changeWifiChannel() {
    if(curChannel > MAX_CHANNELS){ 
        curChannel = 1;
    }
    
    esp_wifi_set_channel(curChannel, WIFI_SECOND_CHAN_NONE);
    delay(100);
    curChannel++;
}

void displayFoundMac() {
    display.setTextSize(1);
    display.setTextColor(WHITE);
    
    delay(100);
    display.clearDisplay();

    display.setCursor(0, 6);
    display.println("Active Clients");
    display.setCursor(90, 6);
    display.println(String(currentPage) + "/" + String(getMaxPages()));

    int row = 0;
    int startIndex = (currentPage - 1) * MAX_MACS_ON_SCREEN;
    int endIndex = startIndex + MAX_MACS_ON_SCREEN;
    for (int i = startIndex; i < endIndex; i++) {
        if (i < macArray.size()) {
            display.setCursor(0, (row * 9) + 16);
            display.println(macArray[i]);
            row++;
        }
    }
    display.display();
}

boolean wasButtonReleased(bool isRight, int reading) {
    if (lastRightButtonRead == 1 && reading == 0 && isRight) {
        return true;
    } else if (lastLeftButtonRead == 1 && reading == 0 && !isRight) {
        return true;
    }
    return false;
}

boolean hasMoreMACPages() {
   return currentPage < getMaxPages();
}

boolean hasLowerMACPages() {
   return currentPage > 1;
}

void loop() {
    changeWifiChannel();

    int leftButtonRead = digitalRead(BUTTON_LEFT_GPIO);
    int rightButtonRead = digitalRead(BUTTON_RIGHT_GPIO);

    if (wasButtonReleased(true, rightButtonRead) && hasMoreMACPages()) {
        currentPage++;
    } else if (wasButtonReleased(false, leftButtonRead) && hasLowerMACPages()) {
        currentPage--;
    }
    
    lastRightButtonRead = rightButtonRead;
    lastLeftButtonRead = leftButtonRead;

    displayFoundMac();
}
```

# Wrapping Up
We've covered a lot of ground in this article and I think now is a good time to break. In the next article we are going to be improving our code to drop off MACs it hasn't seen in a few minutes and grab more information from the WiFi packets we see. Eventually we will work up to building a deauth device, going over how deauth attacks work and why we can do them without any authentication. If you have any questions or need any help getting your ESP32 setup, don't hesitate to reach out via email or Twitter. As always, thank you for reading and happy hacking!