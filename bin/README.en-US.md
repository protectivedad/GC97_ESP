## Firmware

In the current version, only the main code needs to be flashed for correct operation. This can be done with any utility for flashing ESP, via Arduino IDE, etc. I recommend using [Flash download tools](../utils/).
- **GC97_ESP.bin** -- Primary Firmware (address 0x0000000) -- can be used for primary firmware and also for upgrades;

1. Run the firmware utility, specify the ESP8266 controller and select the firmware file (first term):

![alt Firmware](../manual/manual_flashing_single.png)

2. At the bottom, select the COM port to which the controller is connected and press the "START" button;
3. After flashing is complete, close the utility and reboot the controller; If desired, the controller operation can be monitored using any COM port monitoring utility;
4. Find an open access point named GC97-XXXXXXXX, where XXXXXXXX is the last characters of the mac address of your ESP board; Connect to this access point (no password by default);
5. Open the page <http://192.168.4.1> in your browser; the interface should be displayed -- click "Settings" in the top line;

![alt Wifi/AP Settings](../manual/manual_options_wifi.png)

6. Specify the network name and password (case sensitive) of your WiFi network; Save the settings, wait 15-20 seconds;
7. Reboot the ESP and wait for it to come online (may take up to 30 seconds);
8. Open <http://192.168.4.1> and make sure WiFi is connected and the controller has been assigned an IP address on your network (e.g. 192.168.1.100); Otherwise -- check your network settings (item 6);
9. You can now disconnect from the GC97-XXXXXXXX network and open a full interface at the new address given to the controller on your WiFi network (e.g. 192.168.1.100).

Alternatively, if you are using Windows, you can skip steps 8-9 and browse your local network devices. This is usually a lengthy process, but this is also how you can find out the address of the controller and open the control panel just by clicking. GC97 will appear in the list of network devices:

![alt Network device](../manual/manual_gc97_network.png)

Context menu:

![alt Properties](../manual/manual_ssdp.png)

Alternatively, if the device works fine and you want to update the firmware over the air (OTA), you can do it from the console by running the command from the folder where the firmware file is located: `curl -F "image=@GC97_ESP.bin" <ip-адрес>/update`, where _<ip-адрес>_ should be replaced with yours.

---

## Change history

- [*240217*]
  - attempt to fix web server operation (data transfer failure, incomplete download - case #7);
  - minor optimization of web content: removed embellishments in the form of status icons, minification of styles and scripts, return to a single-page cached document;
  - auto-reload the page when a new version is detected to update the cache;
  - small fixes for controller restart (web interface);
  - small optimization of connection to MQTT, retries connection in case of connection loss (within a couple of minutes, case #8);
  - minor changes in sensor declaration in Hass Discovery mode (case #9);
  - normal mode of data output via MQTT: separate sensors + shared json;

- [*231130*](previous/231130/)
  - fixed an error with displaying negative temperatures (#5);
  - The MQTT server can be specified as a regular web address (was only allowed as an IP address until now);
  - small optimization of connection to MQTT, retries connection in case of failures (with interval from a couple of minutes);

- [*231023*](previous/231023/)
  - small optimization of the web server, fixed caching of static pages;
  - HASS Discovery: changed topic names, added polling frequency option;

- [*231020*](previous/231020/)
  - HASS Discovery option has been added;
  - UI page changes: split into html, css, js (all parts are cached), which improves responsiveness a bit;

- [*230526*](previous/230526/)
  - corrected the settings form (there was a field length limitation of 16 characters, case #4);
  - auto-enable the access point if it fails to connect to a wifi network;
  - fixed crash when calling mDNS when network protocols are not initialized correctly (no correct settings);


- [*230420*](previous/230420/)
  - Optimized single file firmware option: no SPIFFS required, simplified initial flashing and upgrades;

- [*230418*](previous/230418/)
  - Adaptation of UI for mobile devices, small edits to the settings web page;
  - Links to the actual firmware files in the update dialog;
  - Fixed bug with firmware/filesystem update via dialog (#1);
  - Added single file flashing version;

- [*230417*](previous/230418/)
  - Added support for multi-lingual UI, available languages: English, Russian;
  - Fixed bugs in UI with dialogs, saving empty settings;
  - A new way for a web server to buffer file uploads;

- [*230415*](previous/230415/)
  - Added SSDP support (detected on the network as a GC97 device);
  - Fixed possible controller crash/reboot when there is no WiFi connection;
  - Corrections to the main web page;
  - Expanded settings block (increased text fields for addresses, names, etc., #3);

- [*210820*](previous/210820/)
  - Fixed flaws in the simplified customization page;

- *210514*
  - Added a simplified setup page for cases where there is no file system;
  - Small edits to the UI;

- *210508*
  - UI fixes, file system firmware update;

- *210505*
  - UI: Saving settings from web, updates in Wifi network scanning dialog;

- *210503*
  - Updates to the json data;

- *210430*
  - UI: Custom dialogs, base UI update;

- *210429*
  - UI: Settings dialog, reboot button;

- *210421*
  - Test version of the firmware.
