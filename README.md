# pv-remote-display

## Remote Statusanzeige für Photovoltaik Anlage
Dieses Projekt wurde initiert um die Werte von openDTU auf einen Remote Display darzustellen. 
Grundsätzlich sind aber alle Werte die per MQTT verfügbar sind anzeigbar.

![Remote-Display](/images/Remote-Display-Front.jpg)


# Hardware
Was wird benötigt
- Wemos D1 Mini

![Remote-Display](/images/wemos-d1-mini.png)

- ST7735S 1.8' TFT Display 128x160

![ST7735S-Front](/images/st7735s.png)
![ST7735S-Back](/images/st7735s-back.png)

- Das Gehäuse ist von [Thingiverse](https://www.thingiverse.com/thing:4965564)

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
3. Nach dem Neustart mit dem WLAN "ESP_Easy_0" verbinden. Kennwort: configesp
4. Jetzt eine Verbindung mit dem lokalen WLAN herstellen
5. Neustart und eine Verbindung mit dem Browser herstellen


# Konfiguration Display
Unter Device "Add" drücken und ein Device "Display - ST77xx TFT" anlegen.

`Name: ST7735S
Enable: Yes
GPIO -> CS: GPIO-4 (D2)
`

![Display-Config](/images/Config-Display.PNG)




