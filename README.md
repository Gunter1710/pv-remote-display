# pv-remote-display

## Remote Statusanzeige für Photovoltaik Anlage
Dieses Projekt wurde initiert um die Werte von openDTU auf einen Remote Display darzustellen. 
Grundsätzlich sind aber alle Werte die per MQTT verfügbar sind anzeigbar.

![Remote-Display](/images/Remote-Display-Front.jpg)

# Hardware
Was wird benötigt
- Wemos D1 Mini
- ST7735S 1.8' TFT Display 128x160
- Das Gehäuse ist von [Thingiverse](https://www.thingiverse.com/thing:4965564)

# Schaltplan
![Schaltplan](https://github.com/Gunter1710/pv-remote-display/assets/49900099/bdeb87af-90c3-4c78-9f0b-920a084e3381)

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



