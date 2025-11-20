+++
date = '2025-11-18'
draft = false
tags = ['Home Assistant', 'Stack M5', 'Core2 V1.1', 'LVGL', 'ESPHome', 'Braucht kein Mensch, muss ich haben', 'Hello World']
title = 'Einleitung Stack M5 Core2 V1.1'
categories = ['Blog']
+++

## Aller Anfang ist schwer

Auch wenn ich in dem [Video](https://www.youtube.com/watch?v=Z0hs0G1mfqU) von Smarty Van gesehen habe, was das Display alles kann und auch wenn ich mir das [Github Gist](https://gist.github.com/SmartyVan/9583deff2d1281fb714d711ee8e83a78) angeschaut habe, einfach war der Einstieg mit diesem Display nicht. 

Klar ESPHome war nichts Neues für mich. Ich werde an dieser Stelle auch nicht auf ESPHome eingehen, da es meiner Meinung nach genug Youtube Videos und Blog Einträge zu diesem Thema gibt. Dennoch war es für mich nicht ganz so einfach wenigstens mal ein 'Hello World' anzuzeigen. Deshalb werde ich hier einfach mal meine Basis Konfiguration zeigen und erklären. 

## Basis Einstellungen

Nachfolgend werde ich hier die einzelnen 'Komponenten' Schritt für Schritt einrichten. Am Schluss sollten alle Funktionen des M5 Stack Core2 V1.1 eingerichtet sein. Für die Batterie-Erweiterung werde ich dann nochmal einen extra Blogeintrag machen. 

### Board Einstellungen

Bei den Board Einstellungen kann man bei ESPHome mittlerweile zwischen ```Arduino``` und ```IDF``` auswählen. Diese beiden Software-Stacks sind aber nicht ganz austauschbar. Wird nichts speziell angegeben, so wird aktuell noch arduino ausgewählt, damit auch bei späteren Updates keine Probleme gibt, geben wir hier gleiche explizit den Typen mit an. 

Das Board ist der ```m5stack-core2```

 ```YAML
 esp32:
  board: m5stack-core2
  framework:
    type: arduino
 ```

### SPI und I2C Einstellungen

Für die Kommunikation mit dem Touchscreen und dem Akku ist der I2C Bus wichtig. Der SPI Bus wird für das Display verwendet. 

```YAML
spi:
  clk_pin: GPIO18
  mosi_pin: GPIO23
  miso_pin: GPIO38

i2c:
  - id: bus_a
    sda: GPIO21
    scl: GPIO22
    scan: True
```

### Touchscreen

Hier wird nun der Touchscreen eingerichtet. Damit dieser funktionieren kann wird jetzt der I2C Bus angeschlossen werden. Die ID des Touch Screens wird nachher in der LVGL Bibliothek verwendet werden.

```YAML
touchscreen:
  - platform: ft63x6
    id: touch_screen
    i2c_id: bus_a
```

{{< alert >}}
**Warning!** Damit der Touchscreen ausgelesen werden kann muss zuvor der I2C Bus eingerichtet werden. Siehe Abschnitt zuvor. 
{{< /alert >}}

### Display

Hier wird das Display angeschlossen. Hierfür wird die Platform ```mipi_spi``` verwendet. 

```YAML
display:
  - platform: mipi_spi
    model: M5CORE
    dimensions: 320x240
    invert_colors: False
    cs_pin: GPIO5
    dc_pin: GPIO15
```

{{< alert >}}
**Warning!** Damit das Display angesteuert werden kann muss zuvor der SPI Bus eingerichtet werden. Siehe Abschnitt zuvor. 
{{< /alert >}}

### LVGL

Diese Bibliothek erlaubt es uns Widgets auf das Display zu zeichnen. Damit wir beim Drücken der Widgets auch Aktionen auslösen können, müssen wir auch den Touchscreen anschließen. 

Unser erstes Widget ist dabei das gute alte 'Hello world'. 

```YAML
lvgl:
  touchscreens:
    touchscreen_id: touch_screen
  widgets:
    - label:
        align: CENTER
        text: "Hello World!"
```

### Sensoren

Im Anschluss werden wir nun die Multi-Purpose Unit (MPU) und den Akku anschließen, so dass wir diese Werte auch später im Home Assistant anzeigen können. 

```YAML
external_components:
  - source: github://stefanthoss/esphome-axp2101
    components: [axp2101]

sensor:
  - platform: axp2101
    model: M5CORE2
    address: 0x34
    i2c_id: bus_a
    update_interval: 30s
    brightness: 75%
    battery_voltage:
      name: "Battery Voltage"
    battery_level:
      name: "Battery Level"
    battery_charging:
      name: "Battery Charging"

  - platform: mpu6886
    address: 0x68
    update_interval: 1s
    accel_x:
      name: "MPU6886 Accel X"
    accel_y:
      name: "MPU6886 Accel Y"
    accel_z:
      name: "MPU6886 Accel Z"
    gyro_x:
      name: "MPU6886 Gyro X"
    gyro_y:
      name: "MPU6886 Gyro Y"
    gyro_z:
      name: "MPU6886 Gyro Z"
    temperature:
      name: "MPU6886 Temperature"
```

#### Akku auslesen

Der verbauten Akku im Stack kann ausgelesen werden und dann auch im Home Assistant gepublished werden. Hierfür ist eine externe Komponente. Diese kann über ```external_components``` 'heruntergeladen' werden. Die Source ist unten aufgelistet. Anschließend können die einzelnen Entities des Akku angegeben werden. 

{{< alert >}}
**Warning!** Damit der Akku ausgelesen werden kann muss zuvor der I2C Bus eingerichtet werden. Siehe Abschnitt zuvor. 
{{< /alert >}}

#### MPU

Die MPU gibt uns die Beschleunigungs-, Drehraten und auch die Temperaturdaten des Sensors zurück. Die Temperatur ist so ungenau, dass man ihn eigentlich nicht für die Home Automatisierung sinnvoll nutzen kann. Der Vollständigkeit halber ist aber aber dennoch hier aufgelistet. 

Der Beschleunigungssensor kann später dafür verwendet werden den Inhalt des Displays zu drehen, oder sogar ganz zu ändern wenn die Orientierung geändert wird. 

### Binärer Sensor / Touch Buttons

Unten am Display gibt es drei digitale Buttons. Diese Buttons werden nachfolgend konfiguriert. Weiter werden die Buttons auch in Home Assistant weitergegeben. Das ermöglicht es später die Buttons für die Home Automatisierung zu nutzen.

```YAML
binary_sensor:
  # the virtual buttons--coordinates taken from
  # https://github.com/m5stack/M5Core2/blob/0134dd3a38cfd335a1ec39da2c149f88baf54326/src/M5Core2.h#L54-L56
  # and
  # https://github.com/m5stack/M5Core2/blob/0134dd3a38cfd335a1ec39da2c149f88baf54326/src/utility/M5Button.h#L811-L815
  # for the parameter order `(x, y, width, height)`
  - platform: touchscreen
    name: Button A
    x_min: 10
    x_max: 120
    y_min: 240
    y_max: 280
    use_raw: true

  - platform: touchscreen
    name: Button B
    x_min: 130
    x_max: 200
    y_min: 240
    y_max: 280
    use_raw: true

  - platform: touchscreen
    name: Button C
    x_min: 230
    x_max: 310
    y_min: 240
    y_max: 280
    use_raw: true
```

## Basis Konfiguration 'Hello World'

Das ist meine Basis Konfiguration. Wenn ihr diese auch verwendet, dann solltet ihr nach dem Einspielen ein 'Hello World' auf dem Display sehen. Weiter müssten die Daten der MPU, des Akkus und der Buttons in Home Assistant geliefert werden.

```yaml
substitutions:
  devicename: m5core2
  upper_devicename: M5Core2 V1.1

esphome:
  name: esphome-web-d62fd8
  friendly_name: M5Core2 V1.1
  libraries: xpower=https://github.com/lewisxhe/XPowersLib.git#v0.2.6
  platformio_options:
    upload_speed: 460800

esp32:
  board: m5stack-core2
  framework:
    type: arduino

psram:
  mode: quad
  speed: 80MHz

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  ap:
    ssid: $devicename Fallback Hotspot
    password: !secret wifi_password

captive_portal:

web_server:
  port: 80

logger:
  level: INFO

api:

ota:
  - platform: esphome
    password: ota

external_components:
  - source: github://stefanthoss/esphome-axp2101
    components: [axp2101]

sensor:
  - platform: axp2101
    model: M5CORE2
    address: 0x34
    i2c_id: bus_a
    update_interval: 30s
    brightness: 75%
    battery_voltage:
      name: "Battery Voltage"
    battery_level:
      name: "Battery Level"
    battery_charging:
      name: "Battery Charging"

  - platform: mpu6886
    address: 0x68
    update_interval: 1s
    accel_x:
      name: "MPU6886 Accel X"
    accel_y:
      name: "MPU6886 Accel Y"
    accel_z:
      name: "MPU6886 Accel Z"
    gyro_x:
      name: "MPU6886 Gyro X"
    gyro_y:
      name: "MPU6886 Gyro Y"
    gyro_z:
      name: "MPU6886 Gyro Z"
    temperature:
      name: "MPU6886 Temperature"

spi:
  clk_pin: GPIO18
  mosi_pin: GPIO23
  miso_pin: GPIO38

i2c:
  - id: bus_a
    sda: GPIO21
    scl: GPIO22
    scan: True

display:
  - platform: mipi_spi
    model: M5CORE
    dimensions: 320x240
    invert_colors: False
    cs_pin: GPIO5
    dc_pin: GPIO15

touchscreen:
  - platform: ft63x6
    id: touch_screen
    i2c_id: bus_a

binary_sensor:
  # the virtual buttons--coordinates taken from
  # https://github.com/m5stack/M5Core2/blob/0134dd3a38cfd335a1ec39da2c149f88baf54326/src/M5Core2.h#L54-L56
  # and
  # https://github.com/m5stack/M5Core2/blob/0134dd3a38cfd335a1ec39da2c149f88baf54326/src/utility/M5Button.h#L811-L815
  # for the parameter order `(x, y, width, height)`
  - platform: touchscreen
    name: Button A
    x_min: 10
    x_max: 120
    y_min: 240
    y_max: 280
    use_raw: true

  - platform: touchscreen
    name: Button B
    x_min: 130
    x_max: 200
    y_min: 240
    y_max: 280
    use_raw: true

  - platform: touchscreen
    name: Button C
    x_min: 230
    x_max: 310
    y_min: 240
    y_max: 280
    use_raw: true

font:
  - file: "gfonts://Roboto"
    id: roboto
    size: 24

lvgl:
  touchscreens:
    touchscreen_id: touch_screen
  widgets:
    - label:
        align: CENTER
        text: "Hello World!"

```