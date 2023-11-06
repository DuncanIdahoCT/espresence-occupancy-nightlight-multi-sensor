# espresence-occupancy-nightlight-multi-sensor
## A hidden (in plain sight) Home Assistant integrated mmWave presence sensor with a PIR for fast motion integrated into a commercially available Night Light! Complete with a BLE Tracker and BT Proxy (optional in config) ability.

![Nightlight Multi-Sensor](/static/images/finished%20product.jpg)

You can see my previous prototype work on a basic version of this with an ESP8266, and eventually a working model with an ESP32-S2 MCU:
  https://github.com/DuncanIdahoCT/espresence-mmwave-nightlight-sensor

For this improved model I chose to use a full featured ESP32 that has a dual radio capable of simultaneous WiFi connectivity and both BLE and BT (classic) so it can function as an optional BLE Tracker and / or Bluetooth Proxy.

I won't go through all the build notes and history here since that is covered in detail in the above linked repo but I'll cover the details of the parts used and how I modified the Night Light which, sadly, seems to be currently out of stock everywhere, I have a few more on backorder so I can make some of these for others or whatever.

For the Night Light I chose to use this unit from Link2Home which sells under some other brands too but the Link2Home model is just better and the case comes apart with zero fuss and only a handful of easy to remove screws:

  https://www.amazon.com/Link2Home-EM-PL62W-Photocell-Change-Charger/dp/B07BFFGXJ9

For the ESP MCU I chose to get an M5Stack Atom Lite, be careful you don't end up with the AtomS3 Lite variant as the ESP32 module is not dual radio and it won't do BT proxy but otherwise should work the same:

  https://shop.m5stack.com/products/atom-lite-esp32-development-kit

![M5Stack Atom Lite](/static/images/M5Stack%20Atom%20Lite%20(esp32%20pico).jpg)

Why this particular ESP32...? well I wanted the Adafruit ESP32 Pico which is a tad bit smaller but simply not available. In fact there are very few Pico form-factor full featured ESP32 dev kits out there. But at $7.50, the M5Stack Atom Lite is a bargain, and the case snaps apart super easy!

![M5Stack Atom Lite Case](/static/images/empty%20atom%20case.jpg)

After the Atom Lite is out of its case, I removed the headers and the Stemma JST connector which provides easy access to some nice solder points for wires and 5V power:

![M5Stack Atom Lite Mods](/static/images/remove%20these.jpg)
![M5Stack Atom Lite Mods](/static/images/back%20of%20esp32%20mcu.jpg)

Note: Removing the Stemma (JST) connector might seem difficult but you can actually just lift it slowly from the white plastic body, and the way it's surface soldered onto the pads, allows for it to simply be peeled off as it were.

The mmWave Sensor I use is pretty common and popular but there are other options out there, this is the one I prefer:

  https://www.dfrobot.com/product-2282.html

![M5Stack Atom Lite Mods](/static/images/connecting%20radar%20to%20mcu.jpg)

And for the PIR, there are analog and digital options but to make the input pulldown work seamlessly I chose the highly recommended Panasonic line and specifically the low profile EKM line:

  https://industrial.panasonic.com/sa/products/pt/papirs/models/EKMC1607111

![M5Stack Atom Lite Mods](/static/images/PIR%20sensor.jpg)

You have several GPIO pins to choose from on the ESP dev kit board, these are the connections I made:

  ESP G    ->   mmWave G
  ESP 5V   ->   mmWave V
  ESP G21  ->   mmWave RX
  ESP G22  ->   mmWave TX
  ESP G25  ->   mmWave IO1

The IO1 is for the binary_sensor and the RX/TX are for programming the settings and controlling the LED on the mmWave, you don't need it for basic operation but you'll likely need to change the range settings and you'll very quickly find the blinking red LED on the mmWave to be an annoyance.

The PIR connection is super simple, power, which you can get from the 5V source or in my case I used the 3V from the ESP because I was looking for an easy solder spot that wasn't already used... and one GPIO:

  ESP G33  ->   PIR signal (middle pin)

Connections overview:

![M5Stack Atom Lite Mods](/static/images/project%20overview.jpg)

Mounting the PIR is fairly easy, drill a hole in a location off to the side and if you prefer, as I did, at an angle aligned with the curve of the plastic night light diffuser:

![M5Stack Atom Lite Mods](/static/images/PIR%20drilled%20and%20installed.jpg)

The nice thing about the low profile model of the Panasonic PIR is that it has a thicker ring at the base, so a smaller hole is required and the base prevents it from falling out, provides easy of gluing and also hides the imperfections of the drilling especially if you ensure you get the white model <- hint!!!

### Installation:
 * Download these files and copy them (keeping their subfolder paths) into your Home Assistant config/esphome main folder:

   ```
   header/leapmmw_sensor.h
   
   packages/leapmmw_sensor.yml
   ```
 
 * In Home Assistant add-on, click ESPHome>open web gui and create a new device choosing the "continue" option and give it a name such as:

   ```
   espresence-occupancy-nightlight-multi-sensor
   ```

* Click next and chose the type of ESP module you used in your build, this isn't a critical thing to have match but as long as it's some kind of ESP32 you can just select that for now and click next.
* You'll see a new card appear in the background for your ESP device, this is just an empty shell with only basic initialization code so far... click skip because you don't want to install this basic code to the ESP quite yet.
* Now click edit on your new sensor in ESPHome and you'll see the basic code:
   ```
   esphome:
    name: espresence-occupancy-nightlight-multi-sensor

   esp32:
    board: esp32dev
    framework:
      type: arduino

   # Enable logging
   logger:

   # Enable Home Assistant API
   api:
     encryption:
       key: "your-key-here"

   ota:
     password: "your-password-here"

   wifi:
    ssid: !secret wifi_ssid
    password: !secret wifi_password

     # Enable fallback hotspot (captive portal) in case wifi connection fails
    ap:
      ssid: "Your-New-Esp-Multi-Sensor"
     password: "wifipass"

   captive_portal:
   ```

* The easiest way to proceed is to copy all the code above out to notepad++ or your favorite editor and then paste back in the entire code from:
   ```
   espresence-occupancy-nightlight-multi-sensor.yaml
   ```
* Now just copy some key lines you saved from your new basic config and paste them into the relevant sections of the full config:

   ```
   substitutions:
      # change device name to match your desired name
      device_name: espresence-occupancy-nightlight-multi-sensor
      # change sensor name below to the one you want to see in Home Assistant
      device_name_pretty: ESpresence Occupancy Night Light Multi-Sensor
      # change room name below to the one you want to see in Home Assistant
      room: "Bathroom"
   ```
* The room: "name" is key as it will be the name of each sensor object in HA so if you chose "Office" here, you sensors will be Office Motion, Office Temperature, etc...

* You'll also want to copy the generated api encryption key and ota password into this section of the full code:

   ```
   # Enable Home Assistant API
   api:
      encryption:
         key: "assigned_when_you_add_to_esphome"

   ota:
      password: "assigned_when_you_add_to_esphome"
   ```

* Lastly, in the wifi: section, there is a line that says "use_address: XXX.XXX.XXX.XXX" this is an optional element to workaround typical issues with mDNS and wifi/subnets. Basically if you find your device is always showing as offline in ESPHome or has any issues at all when making changes or updating OTA, you'll want this setting. Note: this is not a static IP, it just tells ESPHome to use an IP address to do all OTA work with a given ESP device. You can of course set a static IP but this isn't that. You'll also notice that the wifi ssid and passwords are variables, you'll also see at the top of the full config are references to "secrets" file. You'll need to create one or update the one in this repo and copy it to the config/esphome main folder... if you have setup other esp devices, changes are you have this already.

   ```
   # Connect to WiFi & create captive portal and web server
   wifi:
      ssid: "${ssid}"
      password: "${wifi_password}"
      use_address: XXX.XXX.XXX.XXX
   ```
A couple notes:
  
  The M5Stack Atom has it's own board name:
    esp32:
      board: m5stack-atom
  
  And the framework when you're using/if you choose to use BLE/BT shoudl be esp-idf, not arduino
    framework:
      type: esp-idf

# Comments are welcome

I've only created a few projects here now, so I'm happy to hear any suggestions.
