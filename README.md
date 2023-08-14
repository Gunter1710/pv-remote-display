# pv-remote-display

## Remote Statusanzeige für Photovoltaik Anlage
Dieses Projekt wurde initiert um die Werte von openDTU auf einen Remote Display darzustellen. 
Grundsätzlich sind aber alle Werte die per MQTT verfügbar sind anzeigbar.

![Remote-Display](/images/Remote-Display-Front.jpg)


# Hardware
Was wird benötigt
- **Wemos D1 Mini**

![Remote-Display](/images/wemos-d1-mini.png)

- **ST7735S 1.8' TFT Display 128x160**

![ST7735S-Front](/images/st7735s.png)
![ST7735S-Back](/images/st7735s-back.png)

- Das **Gehäuse** ist von [Thingiverse](https://www.thingiverse.com/thing:4965564)

![1.8''TFT + Wemos D1 mini housing](/images/case.png)


# Schaltplan

![Schaltplan](/images/Schaltplan.png)


# Verdrahtung
Von dem ST7735S gibt es diverse Ausführungen. Hier die Verdrahtung meines ST7735S

|TFT|Wemos|
|----|----|
|GND|GND|
|VCC|3V3|
|SCL|D5|
|SDA|D7|
|RES|D0|
|DC|D1|
|CS|D2|
|BL|3V3|


# Software
1. Als Software auf dem Wemos D1 Mini wird [ESPEasy](https://github.com/letscontrolit/ESPEasy) von LETS CONTROL IT verwendet.Hier das neuste Release herunterladen und die Datei ESP_Easy_mega_xxxxxxxx_display_ESP8266_4M1M.bin verwenden
2. Am einfachten geht das flashen mit [NodeMCU-PyFlasher](https://github.com/marcelstoer/nodemcu-pyflasher/releases)
3. Nach dem Neustart mit dem WLAN **Display** verbinden. Kennwort: **configesp**
4. Jetzt eine Verbindung mit dem lokalen WLAN herstellen
5. Neustart und eine Verbindung mit ESPEsay über dem Browser herstellen
6. Im ESPEasy unter **Tools** und dann **Advanced** folgende Einstellungen vornehmen:

|Variable|Eintrag|
|---|---|
|Rules:|Yes|
|Use NTP:|Yes|
|NTP Hostname:|de.pool.ntp.org|
|Timezone Offset (UTC +):|120 [minutes]|


# Konfiguration Hardware
Nun muss ESPEasy konfiguriert werden. Als erstes müssen die voreingestellten GPIOs für das I2C Interface entfernt werden.

|Variable|Eintrag|
|---|---|
|GPIO ⇄ SDA:|-None-|
|GPIO → SCL:|-None-|
|Init SPI:|Yes|

# Verbindung zum openDTU
## MQTT-Broker
Damit die Werte von der openDTU angezeigt werden können muss ein MQTT-Broker verwendet werden. Dazu kann man einen öffentlichen MQTT-Broker wie test.mosquitto.org verwenden oder einen eigenen MQTT-Broker in seinem loken Netzwerk. Eigige Smarthome Systeme stellen einen eigenen MQTT-Broker bereit. Ich werde hier nicht weiter auf das Thema MQTT-Broker eingehen, da es sehr umfangreich ist und es genügend Informationen im Internet dazu gibt.

Um eine Verbindung von ESPEasy und dem MQTT-Broker herzusetllen muss über den Reiter **Controller** und dann **Add** ein **Domoticz MQTT** angelegt werden

|Variable|Eintrag|
|---|---|
|Protocol:|Domoticz MQTT|
|Controller IP:|[Die IP-Adresse des MQTT-Brokers]|
|Enable:|Yes|



# Konfiguration Display
Jetzt legen wir das Device in ESPEsay unter **Device**, dann **Add** drücken und ein Device **Display - ST77xx TFT** anlegen.

![Display-Config](/images/Config-Display.PNG)

|Variable|Eintrag|
|---|---|
|Name:|ST7735S|
|Enable:|Yes|
|GPIO -> CS:|GPIO-4 (D2)|
|GPIO -> DC:|GPIO-5 (D1)|
|GPIO -> RES:|GPIO-16 (D0)|
|TFT display model:|ST7735 128 x 160px|




# Konfiguration MQTT Import
Nun werden die Devices zum Import der openDTU Werte angelegt. Das machen wir unter **Device**, dann **Add** drücken und ein Device **Generic - MQTT Import** anlegen.

![MQTT-Import1](/images/MQTT-Import1.PNG)

|Variable|Eintrag|
|---|---|
|Name:|openDTU1|
|Enable:|Yes|
|Parse JSON messages:|Yes|
|Prefix for all topics:|solar/|

|MQTT Topic|Topic|JSON Attribute|
|---|---|---|
|1|victron/HQxxxxxxKRR/V|V|
|2|powermeter/powertotal|powertotal|
|3|ac/power|power|
|4|victron/HQxxxxxxKRR/PPV|V|

|Values|Name|Decimals|
|---|---|---|
|1|Batt|1|
|2|Netz|0|
|3|HM|0|
|4|PV|0|



Da wir pro MQTT Import Device nur 4 Topics anlegen dürfen muss noch ein zweites  **Generic - MQTT Import** Device anlegelegt werden.

![MQTT-Import2](/images/MQTT-Import2.PNG)

|Variable|Eintrag|
|---|---|
|Name:|openDTU2|
|Enable:|Yes|
|Parse JSON messages:|Yes|
|Prefix for all topics:|solar/|

|MQTT Topic|Topic|JSON Attribute|
|---|---|---|
|1|114181027126/status/limit_absolute|limit_absolute|
|2|114xxxxxxxxx/0/yieldday|yieldday|
|3|victron/HQxxxxxxKRR/H20|H20|
|4|||

|Values|Name|Decimals|
|---|---|---|
|1|limit_absolute|0|
|2|yieldday|0|
|3|H20|3|
|4|Value4|2|


