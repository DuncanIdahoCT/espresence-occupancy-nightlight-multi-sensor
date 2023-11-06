# espresence-occupancy-nightlight-multi-sensor
## A hidden (in plain sight) Home Assistant integrated mmWave presence sensor with a PIR for fast motion integrated into a commercially available Night Light! Complete with a BLE Tracker and BT Proxy (optional in config) ability.

![Nightlight Multi-Sensor](/static/images/finished%20product.jpg)

You can see my previous prototype work on a basic version of this with an ESP8266, and eventually a working model with an ESP32-S2 MCU:
  https://github.com/DuncanIdahoCT/espresence-mmwave-nightlight-sensor

For this improved model I chose to use a full featured ESP32 that has a dual radio capable of simultaneous WiFi connectivity and both BLE and BT (classic) so it can function as an optional BLE Tracker and / or Bluetooth Proxy.

I won't go through all the build notes and hisotry here since that is covered in detail in the above linked repo but I'll cover the details of the parts used and how I modified the Night Light which, sadly, seems to be currently out of stock everywhere, I have a few more on backorder so I can make some of these for others or whatever.

For the Night Light I chose to use this unit from Link2Home which sells under some other brands too but the Link2Home model is just better:

  https://www.amazon.com/Link2Home-EM-PL62W-Photocell-Change-Charger/dp/B07BFFGXJ9

For the ESP MCU I chose to get an M5Stack Atom Lite, be careful you don't end up with the AtomS3 Lite variant as the ESP32 module is not dual radio and it won't do BT proxy but otherwise should work the same:

  https://shop.m5stack.com/products/atom-lite-esp32-development-kit

![M5Stack Atom Lite](/static/images/M5Stack%20Atom%20Lite%20(esp32%20pico).jpg)

Why this particular ESP32...? well I wanted the Adafruit ESP32 Pico which is a tad bit smaller but simply not available. In fact there are very few Pico form-factor full featured ESP32 dev kits out there. But at $7.50, the M5Stack Atom Lite is a bargain, and the case snaps apart super easy!

![M5Stack Atom Lite Case](/static/images/empty%20atom%20case.jpg)

After the Atom Lite is out of its case, I removed the headers and the Stemma JST connector which provides easy access to some nice solder points for wires and 5V power:

![M5Stack Atom Lite Mods](/static/images/remove%20these.jpg)

The mmWave Sensor I use is pretty common and popular but there are other options out there, this is the one I prefer:

  https://www.dfrobot.com/product-2282.html

And for the PIR, there are analog and digital options but to make the input pulldown work seamlessly I chose the highly recommended Panasonic line and specifically the low profile EKM line:

  https://industrial.panasonic.com/sa/products/pt/papirs/models/EKMC1607111

![M5Stack Atom Lite Mods](/static/images/PIR%20sensor.jpg)

