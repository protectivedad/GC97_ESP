# GC97 coulometer data exchange via ESP8266 microcontroller
### Remote battery charge/discharge/capacity monitoring with web interface settings and MQTT protocol data sending support

![alt GC97 + ESP8266 = MQTT/JSON](manual/GC97_ESP.jpg)

<details>
<summary>Briefly about the GC97</summary>

[Coulometer GC97](https://aliexpress.ru/wholesale?catId=0&SearchText=GC97%20coulometer) -- a simple, Chinese product designed for connection to a rechargeable battery (UPS, Li-Ion, LeFePo4, etc. battery powered devices). Allows you to visually monitor basic characteristics such as voltage, current consumption (power), charge level, residual capacity, etc. Can be set for different batteries with individual parameters and the possibility of precise adjustments. An "alarm" signal output is available to notify, for example, critical discharge or exceeding the allowable load power. But the main beauty of this coulometer is the TTL protocol support. That is, the device can be read digitally, which is great for various systems that must be monitored remotely or be part of the now fashionable "smart home". One simple, cheap and fast solution is the use of microcontrollers based on [ESP8266 and its derivatives](https://aliexpress.ru/wholesale?CatId=0&SearchText=ESP8266+wifi&ltype=wholesale&SortType=total_tranpro_desc&groupsort=1). This allows remote readings from the coulometer via LAN requests (web interface) or integration with a smart home system via the MQTT protocol.

In order to create such a bundle you will need to have:
 - GC97 coulometer
 - ESP8266 controller (in any form available to you, from debug versions of the NodeMCU to the miniaturized D1 mini, etc.)
 - provide power to the ESP controller (power can be supplied via USB port, if available, from a separate power supply, external or miniature step-down DC-DC converter; Preferably share a common ground with the battery/coulometer, most importantly, make sure to supply the allowed 3.3V or 5V).

Connect the GC97 to the battery according to the diagram on it and power the ESP. The GC97 itself has Rx/Tx pins, which should be connected to the corresponding ESP pins (if it doesn't work right away, try swapping the Rx/Tx pins). It is quite easy to hide the ESP board (and the miniature DC-DC inside the GC97 case - there is enough space there).
 
You can find overviews, examples of use and connection in YouTube, e.g. on the channel [dima espirans](https://www.youtube.com/watch?v=-X8CvknmFzM).
</details>

---

[**Firmware Download**](bin/)

---

## Recent changes:

- [*240317*](bin/)
  - attempt to fix web server operation (data transfer failure, incomplete download - case #7);
  - minor optimization of web content: removed embellishments in the form of status icons, minification of styles and scripts, return to a single-page cached document;
  - auto-reload the page when a new version is detected to update the cache;
  - small fixes for controller restart (web interface);
  - small optimization of connection to MQTT, retries connection in case of connection loss (within a couple of minutes, case #8);
  - minor changes in sensor declaration in Hass Discovery mode (case #9);
  - normal mode of data output via MQTT: separate sensors + shared json;

- [*231130*](bin/previous/231130/)
  - fixed an error with displaying negative temperatures (#5);
  - The MQTT server can be specified as a regular web address (was only allowed as an IP address until now);
  - small optimization of connection to MQTT, retries connection in case of failures (with interval from a couple of minutes);

- [*231023*](bin/previous/231023/)
  - small optimization of the web server, fixed caching of static pages;
  - HASS Discovery: changed topic names, added polling frequency option;

- [*231020*](bin/previous/231020/)
  - HASS Discovery option has been added;
  - UI page changes: split into html, css, js (all parts are cached), which improves responsiveness a bit;

- [*230526*](bin/previous/230526/)
  - corrected the settings form (there was a field length limitation of 16 characters, case #4);
  - auto-enable the access point if it fails to connect to a wifi network;
  - fixed crash when calling mDNS when network protocols are not initialized correctly (no correct settings);

- [*230420*](bin/previous/230420/)
  - Optimized single file firmware option: no SPIFFS required, simplified initial flashing and upgrades;
  - Minor fixes to web server performance;

- [*230418*](bin/previous/230418/)
  - Adaptation of UI for mobile devices, small edits to the settings web page;
  - Links to the actual firmware files in the update dialog;
  - Fixed bug with firmware/filesystem update via dialog (#1);
  - Added single file flashing version;

- [*230417*](bin/previous/230418/)
  - Added support for multi-lingual UI, available languages: English, Russian;
  - Fixed bugs in UI with dialogs, saving empty settings;
  - A new way for a web server to buffer file uploads;

- [*230415*](bin/previous/230415/)
  - Added SSDP support (detected on the network as a GC97 device);
  - Fixed possible controller crash/reboot when there is no WiFi connection;
  - Corrections to the main web page;
  - Expanded settings block (increased text fields for addresses, names, etc., #3);

---

So, here you will find the firmware of ESP8266 to work with GC97 coulometer, which I created for my personal needs. This is my first ESP firmware and experience of this kind, so there may be some errors and mistakes. But it is what it is :)

*The following features are available in the firmware*:
- AP (wifi access point) -- for independent operation and initial configuration. By default, an open (no password) network is created with the name GC97-XXXX (where XXXX is the last characters of the ESP mac address), IP address 192.168.4.1;
- WiFi network connection (ability to scan and select 2.4GHz network, auto-connect on timeout);
- The mDNS local network virtual host (<Имя_точки_AP>.local);
- SSDP support (the device is visible in the local network and you can open the control panel just by clicking);
- Built-in web server with basic coulometer information, web-based configuration, reboot, reset and firmware options; adaptation for display on mobile devices;
- Support for OTA (over-the-air) updates;
- Support for sending data via MQTT broker with the ability to specify server address and port, name, password, topic and Retain flag support; Auto-connect; Optimize sending data on changes only;
- Discovery option is available for Home Assistant: automatically add devices and objects;
- Data query via web in JSON format (by type, general query);
- Selecting the GC97 coulometer interrogation period, monitoring and averaging the data series to smooth out "outliers" in the values;
- Light indication of ESP/connections status and operating mode;
- Output debugging information of the main actions and status of the ESP controller when connected via serial port (connection speed 19200).


## Web interface

![alt Web server main screen](manual/manual_web_main.png)

The page is available:
- by the IP address of the built-in access point (192.168.4.1, after connecting to the GC97-XXXX network);
- by IP address given by DHCP of wifi network (if correct SSID and password are specified), e.g. 192.168.0.100;

This page displays the current status of the coulometer and all connections (data is updated at 10-15 second intervals).

### Settings Dialog:

The built-in access point (AP) settings are available, (if the name is empty, GC97-XXXX will be used, where XXXX is the last characters of the ESP mac address). You can also set the authorization data in WiFi network (priority, recommended mode):

![alt Wifi/AP Settings](manual/manual_options_wifi.png)

Network scanning is available with the ability to select the desired network:

![alt Scanning Wifi networks](manual/manual_scan_networks.png)

You can specify the required MQTT broker settings:

![alt MQTT Settings](manual/manual_options_mqtt.png)

Specify the period of coulometer polling (not more than once per second), the same is the period of data sending via MQTT protocol:

![alt Advanced settings](manual/manual_options_misc.png)

If the "Update only changes" option is enabled, requests to the MQTT broker will be executed only in case of changes and forced once every 10 polling cycles (for cases when retain is not used). You can also disable over-the-air update support (OTA, requires reboot to apply the option). The "Always on LED" option inverts the operation of the built-in LED on the ESP board: constant glow in run mode.

## LED indication

When the ESP boots up, there is a series of short and frequent flashes for a second. Then, at intervals of 2 seconds, single flashes inform about attempts to connect to WiFi network, double flashes - to MQTT server (if parameters of both are specified).

At intervals of the coulometer polling (by default every 10 seconds) you can see the LED flashes. The number indicates the status:
- A single, moderately prolonged flash is normal operation;
- A double flash indicates a Wifi connection problem;
- A triple flash indicates a problem connecting to an MQTT broker;
- Four flashes -- unable to get data from GC97 coulometer (not connected or data is incorrect).

## Coulometer data

All coulometer information as well as most ESP status and setup information is transmitted to the MQTT broker at a specified frequency as json. The same data in json format can be directly requested at any arbitrary moment via a permanent web address:
- http://<ip_address>/get/all.json -- the entire data packet
- http://<ip_address>/get/gc97.json -- coulometer data
- http://<ip_address>/get/esp.json -- ESP status and settings
- http://<ip_address>/get/state.json -- uptime ESP

![alt JSON](manual/manual_json.png)

## HASS Discovery

If you are using the GC97 as part of a Home Assistant-based smart home, the HASS Discovery option is available. When enabled, MQTT will send full descriptions of all sensors and devices. As a result, within seconds you will have 2 new entries in your device list, one for the Coolometer and one for the ESP board:

![alt Device list](manual/manual_hass_devices.png)

It supports operation with multiple devices at the same time. You can also remotely restart the device directly from the smart home interface and monitor its current status.

![alt GC97](manual/manual_hass_gc97.png) ![alt ESP](manual/manual_hass_esp.png)

## Additional commands

Commands are available for web-based control:
- http://<ip_address>/cmd/reset -- reboot (available through the web interface);
- http://<ip_address>/cmd/factory -- reset all settings (can be useful sometimes, but if the controller is still accessible via web :), otherwise only reflashing).

## Firmware

In the current version, only the main code needs to be flashed for correct operation. This can be done with any utility for flashing ESP, via Arduino IDE, etc. I recommend using [Flash download tools](https://www.espressif.com/en/support/download/other-tools).
- **GC97_ESP.bin** -- Main firmware with code (address 0x0000000) -- can also be used for OTA update;

1. Run the firmware utility, specify the ESP8266 controller and select the firmware file (first term):

![alt Firmware](manual/manual_flashing_single.png)

2. At the bottom, select the COM port to which the controller is connected and press the "START" button;
3. After flashing is complete, close the utility and reboot the controller; If desired, the controller operation can be monitored using any COM port monitoring utility;
4. Find an open access point named GC97-XXXXXXXX, where XXXXXXXX is the last characters of the mac address of your ESP board; Connect to this access point (no password by default);
5. Open the page <http://192.168.4.1> in your browser; the interface should be displayed -- click "Settings" in the top line;

![alt Wifi/AP Settings](manual/manual_options_wifi.png)

6. Specify the network name and password (case sensitive) of your WiFi network; Save the settings, wait 15-20 seconds;
7. Reboot the ESP and wait for it to come online (may take up to 30 seconds);
8. Open <http://192.168.4.1> and make sure WiFi is connected and the controller has been assigned an IP address on your network (e.g. 192.168.1.100); Otherwise -- check your network settings (item 6);
9. You can now disconnect from the GC97-XXXXXXXX network and open a full interface at the new address given to the controller on your WiFi network (e.g. 192.168.1.100).

Alternatively, if you are using Windows, you can skip steps 8-9 and browse your local network devices. This is usually a lengthy process, but this is also how you can find out the address of the controller and open the control panel just by clicking. GC97 will appear in the list of network devices:

![alt Network device](manual/manual_gc97_network.png)

Context menu:

![alt Properties](manual/manual_ssdp.png)

Alternatively, if the device works fine and you want to update the firmware over the air (OTA), you can do it from the console by running the command from the folder where the firmware file is located: `curl -F "image=@GC97_ESP.bin" <ip-адрес>/update`, where _<ip-адрес>_ should be replaced with yours.


## Old firmware version (for versions with .spiffs.bin firmware)

[See the description of the older firmwares](bin/previous/230419/)

## Bugs

No critical ones :) Create Issues, if you find any :)

## To-Do / Plans
 
At the moment all basic functionality is implemented. Perhaps, if time permits, more will be added:
- [x] HASS discovery (support Home Assistant Discovery mode);
- [x] MQTT entities -- mode with individual coulometer parameters during MQTT transmission (now only as a json data packet);
- [ ] Additional options in the UI (most are already supported in code) to customize: individual blocks of data given to MQTT; some timeouts and check/update periods; detailing Debug output to serial port (for debugging and process monitoring), and remote viewing of controller logs via web.
- [ ] Blynk support (kinda like a lot of people use it);
- [ ] English translation of the description;
- [ ] Video tutorial for initial setup;
- [ ] Something else, I can't remember right now :)
