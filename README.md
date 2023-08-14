# pv-remote-display

## Remote Statusanzeige für Photovoltaik Anlagen
Dieses Projekt wurde initiiert um die Werte von openDTU auf einen Remote Display darzustellen. 
Grundsätzlich sind aber alle Werte, die per MQTT verfügbar sind, anzeigbar.

![Remote-Display](/images/Remote-Display-Front.jpg)

# Funktionsweise
Die Daten des Hoymiles Wechselrichters und des Shelly3EM werden im openDTU gesammelt und an den MQTT-Broker geschickt. Das Remote Display holt sich diese Daten von dem MQTT-Broker und stellt sie mit Hilfe von ESPEasy dar. Das Remote Display ist aber nicht an diese Konfiguration gebunden. Im Prinzip können alle Daten die per MQTT verfügbar sind dargestellt werden.

![MQTT-Broker](/images/MQTT-Broker.PNG)

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
1. Als Software auf dem Wemos D1 Mini wird [ESPEasy](https://github.com/letscontrolit/ESPEasy) von LETS CONTROL IT verwendet. Hier das neuste Release herunterladen und die Datei ESP_Easy_mega_xxxxxxxx_**display**_ESP8266_4M1M.bin verwenden
2. Am einfachsten geht das flashen mit [NodeMCU-PyFlasher](https://github.com/marcelstoer/nodemcu-pyflasher/releases)
3. Nach dem Neustart mit dem WLAN Name: **"Display"** verbinden. Kennwort: **configesp**
4. Jetzt die Daten für das lokalen WLAN eingeben
5. Neustart und eine Verbindung mit ESPEasy über dem Browser herstellen. Es sollte mit http://display funktionieren. Wenn das nicht funktioniert, dann die IP-Adresse im DHCP Menü eures Routers rausfinden (bei der Fritzbox unter Heimnetz/Netzwerk) 
6. Im ESPEasy Menü unter **Tools** und dann **Advanced** folgende Einstellungen vornehmen:

|Variable|Eintrag|
|---|---|
|Rules:|Yes|
|Use NTP:|Yes|
|NTP Hostname:|de.pool.ntp.org|
|Timezone Offset (UTC +):|120 [minutes]|


# Konfiguration Hardware
Nun muss ESPEasy konfiguriert werden. Als erstes müssen die voreingestellten GPIOs für das I2C Interface entfernt und SPI für das Display aktiviert werden.

|Variable|Eintrag|
|---|---|
|GPIO ⇄ SDA:|-None-|
|GPIO → SCL:|-None-|
|Init SPI:|Yes|

# Verbindung zum openDTU
## MQTT-Broker
Damit die Werte von der openDTU angezeigt werden können muss ein MQTT-Broker verwendet werden. Dazu kann man einen öffentlichen MQTT-Broker wie test.mosquitto.org verwenden oder einen eigenen MQTT-Broker in seinem lokalen Netzwerk. Einige Smarthome Systeme stellen einen eigenen MQTT-Broker bereit. Ich werde hier nicht weiter auf das Thema MQTT-Broker eingehen, da es sehr umfangreich ist und es genügend Informationen im Internet dazu gibt.

Um eine Verbindung von ESPEasy und dem MQTT-Broker herzustellen muss über den Reiter **Controller** und dann **Add** ein **Domoticz MQTT** angelegt werden

|Variable|Eintrag|
|---|---|
|Protocol:|Domoticz MQTT|
|Controller IP:|[Die IP-Adresse des MQTT-Brokers]|
|Enable:|Yes|



# Konfiguration Display
Jetzt legen wir das Device in ESPEasy unter **Device** an. Dann **Add** drücken und ein Device **Display - ST77xx TFT** anlegen.

![Display-Config](/images/Config-Display.PNG)

Jetzt noch die Werte eintragen.

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

Eine gute Hilfe die richtigen MQTT Werte zu finden ist der frei erhältliche [MQTT-Explorer](https://github.com/thomasnordquist/MQTT-Explorer/releases) 

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
|1|114xxxxxxxxx/status/limit_absolute|limit_absolute|
|2|114xxxxxxxxx/0/yieldday|yieldday|
|3|victron/HQxxxxxxKRR/H20|H20|

|Values|Name|Decimals|
|---|---|---|
|1|limit_absolute|0|
|2|yieldday|0|
|3|H20|3|

# Die ESPEasy Rules
Mit Rules in ESPEasy kann man Event getriggert das Display steuert. Die Rules findet ihr [hier](/Rule%20Set%201.txt). Einfach kopieren und in Rule Set 1 kopieren. 

Achtung! Der Reiter **Rule** wird in ESPEasy nur angezeigt wenn vorher der Hacken unter **Tools/Advanced/Rules** gesetzt wurde

Wer die Rules verändern will, oder eigene Rules schreiben will, wird bei ESPEasy fündig. [Display - ST77xx TFT](https://espeasy.readthedocs.io/en/latest/Plugin/P116.html) oder [Rules](https://espeasy.readthedocs.io/en/latest/Rules/Rules.html) im Allgemeinen.

# Verbesserungen
Wie bei jedem Projekt gibt es immer Potential zur Verbesserung. Ihr seid herzlich gerne dazu eingeladen mir Vorschläge zur Optimierung zu schicken. 

Optimierungsbedarf:
- Im Moment wird der Bildschirm alle 10 Sekunden gelöscht. Das ist nötig, da teilweise alte Werte nicht komplett überschrieben werden. z.B. wenn PV einen Wert von 534W hat und bei der nächsten Messung nur noch 46W, dann steht auf dem Display **PV:  46WW**. Ich suche noch einen eleganten Weg nur die alten Werte zu löschen und nicht den ganzen Bildschirm.
- Das 1.8 TFT Display ist zwar ganz nett, aber ein 2.8' Bildschirm wäre schon besser abzulesen. Ich habe noch ein ILI9341 Display (ohne Touch) rumliegen. Vielleicht schaffe ich es in nächster Zeit das zu aktivieren
- Der Wemos und das ST7735S sind ziemliche Stromfresser. Ich hätte gerne eine Version, die mit Batterie/Akku laufen kann.
- In ESPEasy gibt es die Möglichkeit über einen Display-Button verschiedene Bildschirme anzuzeigen. Vorstellbar wäre z.B. Bildschirm aus, oder die einzelnen Werte der Strings/Phase, oder je ein Bildschirm pro Wechselrichter
- Englische Übersetzung. 


# Summary
Mir hat das Projekt richtig Spaß gemacht. Es würde mich freuen, wenn die eine oder andere Verbesserung bzw. Variante entstehen würde. Sofern ihr sie mir zuschickt, würde ich diese hier veröffentlichen und euch erwähnen.

