+++
date = '2025-11-27'
draft = false
tags = ['Home Assistant', 'Stack M5', 'Core2 V1.1', 'LVGL', 'ESPHome', 'Braucht kein Mensch, muss ich haben', 'Erste Schritte', 'Layout']
title = 'Teil 1: LVGL mit ESPHome erste Seite, Layout, Widget und Verbindung mit Home Assistant'
categories = ['Blog']
+++

## Ziel

Nachdem wir beim letzten Mal ein 'Hello World' auf dem Display hatten, soll es heute ein wenig mehr in die Tiefe gehen. Da ich das Display später mal als kleines Dashboard für Home Assistant verwenden will, werden wir hier Werte aus Home Assistant holen und diese mit einem 'Arc' Element anzeigen. Als Startpunkt werde ich hierfür Werte nehmen die man aus dem Energy-Dashboard bereits kennt, z.B. Autarkiegrad, Eigenverbrauch, Energie-Bilanz und den aktuellen Akku-Stand meines Zendure AIO2400 Speichers.

Nur damit ich an dieser Stelle nicht irgendwelche fremden Lorbeeren ernte. Vieles von dem was ich hier in diesem Artikel beschreiben werde ist entweder von dem Youtube Kanal [SmartyVan](https://smartyvan.com) oder zumindest inspiriert davon. Netter weiße hat der Kollege nicht nur Videos gemacht, sondern auch seine komplette [Konfiguration](https://gist.github.com/SmartyVan/9583deff2d1281fb714d711ee8e83a78) auf Github hochgeladen und mit uns geteilt. Da ich es aber schwierig finde, gerade für Anfänger sich in solche eine mächtige Konfiguration einzuarbeiten, werde ich das Schritt für Schritt machen. 

## Anleitung 

### Seite und Layout

Bevor wir uns Gedanken um die einzelnen Widgets machen, schauen wir uns erst einmal die einzelne Seite an. Das ist am Ende die Basis für unser kleines Dashboard. Wir fangen mit unserer ```main_page``` an. Die Dimensionen setzen wir auf die Größe unseres Displays also ```320 x 240```. Den Hintergrund setzen wir auf Schwarz, ```bg_color: 0xFFFFFF```. Die Ränder/Rahmen stellen wir auf durchsichtig ```border_opa: TRANSP``` und scrollen wollen wir unser Layout auch nicht, das Display ist so klein, dass ein überfrachten der Daten keinen Sinn macht, also ```scrollbar_mode: "OFF"```. Mit ```pad_top: 25``` sorgen wir dafür, dass wir oben noch ein wenig Platz haben um unsere Statusbar später anzeigen zu können. 

Nachdem die Seite nun definiert ist, wird es Zeit sich um das eigentlich Layout zu kümmern. Das Layout sorgt dafür, dass unsere Widgets nachher automatisch von oben links nach unten rechts aufgefüllt werden. Für unser erstes Layout haben wir also Platz für 4 Widgets. Das von uns verwendete Layout ist das ```type: FLEX```. ```flex_flow: ROW_WRAP``` hier definieren wir, dass die Widgets von oben links nach unten rechts aufgefüllt werden. Mit ```flex_align_main: SPACE_EVENLY```diesem Attribute werden die Widgets gleichmäßig von rechts nach links verteilt, so dass der Abstand zum Rand und zu den Widgets gleich aufgeteilt ist. Zuletzt ```flex_align_track: START``` hier wird die Verteilung von oben nach unten aufgeteilt, diesmal aber so, dass die Widgets sich eher nach oben orientieren. 

Damit ist unsere erste Seite und das dazugehörige Layout nun fertig konfiguriert. 

```yaml
lvgl:
  pages:
    - id: main_page # Default page
      width: 320
      height: 240
      pad_top: 25
      bg_color: 0xFFFFFF
      border_opa: TRANSP
      scrollbar_mode: "OFF"
      layout:
        type: FLEX
        flex_flow: ROW_WRAP
        flex_align_main: SPACE_EVENLY
        flex_align_track: START
        pad_row: 0
```

### Arc-Widgets

Fangen wir mit unserem ersten Arc-Widget an. Hier will ich den aktuellen State of Charge (SoC), also den Ladestand meines AIO2400 anzeigen. Das Arc Element ist dabei einfach eine Kreisanzeige, welche standardmäßig einen 270° Kreis anzeigt, welcher nach untenhin offen ist. Man kennt solche Anzeigen auch von Apple Watch Complications. Zusätzlich zu dem Arc werden wir auch noch den aktuellen Wert, sowie ein kleines Label anzeigen, damit man auch weiß was dieser Wert nachher aussagt. 

Damit wir nachher den Anzeigewert des Arc auch dynamisch anpassen können braucht dieses Element eine ID, diese können wir frei vergeben. Da ich hier meinen Batteriestand angeben will, habe ich ganz schlicht ```id: battery_arc``` gewählt. Unser Widget soll nachher zentriert sein, weshalb wir bei align auch CENTER auswählen. Die Abmaße von ```105 x 105``` passen gut zum Display, so dass wir nachher auch schön 4 Widgets unterbekommen. ```pad_all: 0```sorgt dafür, dass wir nirgends unnötig Platz durch Padding, also "Sicherheitsabstände" verlieren. Das Display ist so schon klein genug. Da das Arc-Widget, eigentlich zwei Kreise ineinander darstellt, kann man diese auch getrennt voneinander Konfigurieren in Farbe und Größe. Für die Größe wählen wir hier ```arc_width: 14```. Die Farbe muss nicht angegeben werden. In den anderen Arc habe ich spaßeshalber mal eine andere Farbe gewählt, zu sehen unten im Gesamt-YAML. Das geht mit dem ```arc_color: dimgrey```, auf Höhe des ```arc_width``` Attributes. 

Der Bogen ist also geschlagen, jetzt fehlen noch die Labels. Diese werden als Unterelement wiederum in den Arc hinzugefügt. Das geschieht indem man dem Arc nun Widgets zuweist. Damit die Labels nachher untereinander zu sehen sind, verwenden wir ```align_to:```. Das sorgt dafür, dass wir die Elemente voneinander abhängig machen können. Ich kann also sagen ein Label soll relativ zu einem anderen Label untereinander sein, zum Beispiel ```align: OUT_BOTTOM_MID```. Die anderen Attribute sind soweit alle selbsterklärend, einzig die Schrift wir bei dem einen Label explizit gesetzt über ```text_font: roboto```. Mehr zu den Schriftarten in einem weiteren Kapitel.

{{< alert >}}
**Warning!** Die Vorgegebenen Label-Texte werden erst dann geändert wenn nach dem Boot-Prozess eine erfolgreiche Verbindung zu Home Assistent hergestellt werden konnte. 
{{< /alert >}}

```YAML
        - arc:
            id: battery_arc
            align: CENTER
            arc_rounded: false
            width: 105
            height: 105
            pad_all: 0
            arc_width: 14
            indicator:
              arc_rounded: false
              arc_width: 14
            widgets:
              - label:
                  id: battery_label
                  align: CENTER
                  text_align: CENTER
                  y: -8
                  text: "LVL"
                  text_font: roboto
              - label:
                  id: battery_label_value
                  width: 80
                  text_align: CENTER
                  text: "BATT"
                  align_to:
                    id: battery_label
                    align: OUT_BOTTOM_MID
```

### Home Assistant Anbindung und Updaten der Werte

Damit wir nachher auch Werte anzeigen können, benötigen wir ersteinmal die Werte aus Home Assistant. Im Bereich ```platform: homeassistant``` kann nun auf einzelne Entitäten aus Home Assistant rausgelesen werden. In meinem Fall der State of Charge meines Zendure AIO (nicht wundern, meine Entität ist falsch benannt, leider). Ich gehe als her, vergebe dem Sensor eine ID und anschließend hole ich mir mit der entity_id den entsprechenden werde.

Damit dieser Wert später auch auf dem Display sichtbar wird muss ich nun noch definieren was passieren soll wenn sich dieser Wert ändert, das mache ich über ```on_value:```. Hier kann ich nun meinen vorher definierten Arc und den Text meines Labels überschreiben. Sobald mein ESP also Daten von Home Assistant empfangen hat, werden die Default-Werte für den Arc und das Label überschrieben.

```YAML
  - platform: homeassistant
    id: battery
    entity_id: sensor.sf_hub2000_battery_level
    on_value:
      then:
        - lvgl.arc.update:
            id: battery_arc
            value: !lambda return x;
        - lvgl.label.update:
            id: battery_label_value
            text:
              format: "%0.0f %%"
              args: [id(battery).state]
```

## Finaler Code

Hier der Zwischenstand meiner gesamten YAML Datei. Zur Info. Es sind noch ein paar weitere Teile in der YAML drinnen. Diese werde ich zu einem späteren Zeitpunkt erläutern. 

```YAML
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

  manual_ip:
    static_ip: "192.168.178.164"
    gateway: !secret gateway_ip
    subnet: !secret netmask
    dns1: !secret gateway_ip

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Core5"
    password: "uGTBPyGiEQ3X"

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
    id: axp_comp
    i2c_id: bus_a
    update_interval: 30s
    brightness: 75%
    battery_voltage:
      name: "Battery Voltage"
      id: battery_voltage
      device_class: Voltage
      unit_of_measurement: "V"
      state_class: measurement

    battery_level:
      name: "Battery Level"
      id: battery_level_id
      device_class: battery
      unit_of_measurement: "%"
      state_class: measurement
      on_value:
        - lvgl.label.update:
            id: battery_status_label
            text:
              format: "%0.0f%%"
              args: [id(battery_level_id).state]
        - lvgl.label.update:
            id: battery_status
            text: !lambda |-
              static char buf[10];
              std::string icon;
              if (id(battery_is_charging)) {
                  icon = "\U000F0084"; // mdi-battery-charging
              } else if(x == 100.0) {
                  icon = "\U000F0079"; // mdi-battery (full)
              } else if (x > 90) {
                  icon = "\U000F0082"; // mdi-battery-90
              } else if (x > 80) {
                  icon = "\U000F0081"; // mdi-battery-80
              } else if (x > 70) {
                  icon = "\U000F0080"; // mdi-battery-70
              } else if (x > 60) {
                  icon = "\U000F007F"; // mdi-battery-60
              } else if (x > 50) {
                  icon = "\U000F007E"; // mdi-battery-50
              } else if (x > 40) {
                  icon = "\U000F007D"; // mdi-battery-40
              } else if (x > 30) {
                  icon = "\U000F007C"; // mdi-battery-30
              } else if (x > 20) {
                  icon = "\U000F007B"; // mdi-battery-20
              } else if (x > 10) {
                  icon = "\U000F007A"; // mdi-battery-10
              } else if (x > 0) {
                  icon = "\U000F008E"; // mdi-battery-outline
              } else {
                  icon = "\U000F0091"; // mdi-battery-unknown
              }
              snprintf(buf, sizeof(buf), "%s", icon.c_str());
              return buf;

    battery_charging:
      name: "Battery Charging"
      id: battery_charging
      on_state:
        then:
          - lambda: |-
              id(battery_is_charging) = x > 0.5;
          - if:
              condition:
                and:
                  - lvgl.is_paused:
                  - lambda: 'return id(battery_is_charging);'
              then:
                - lvgl.resume:
                - lvgl.widget.redraw:

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

  - platform: homeassistant
    id: battery
    entity_id: sensor.sf_hub2000_battery_level
    on_value:
      then:
        - lvgl.arc.update:
            id: battery_arc
            value: !lambda return x;
        - lvgl.label.update:
            id: battery_label_value
            text:
              format: "%0.0f %%"
              args: [id(battery).state]

  - platform: homeassistant
    id: autarkie
    entity_id: sensor.autarkie
    on_value:
      then:
        - lvgl.arc.update:
            id: autarkie_arc
            value: !lambda return x;
        - lvgl.label.update:
            id: autarkie_label_value
            text:
              format: "%0.0f %%"
              args: [id(autarkie).state]

  - platform: homeassistant
    id: bilanz
    entity_id: sensor.strom_bilanz
    on_value:
      then:
        - lvgl.indicator.update:
            id: bilanz_value
            value: !lambda return x;
        - lvgl.label.update:
            id: bilanz_label_value
            text:
              format: "%0.1f kWh%"
              args: [id(bilanz).state]

  - platform: homeassistant
    id: eigenverbrauch
    entity_id: sensor.eigenverbrauch_2
    on_value:
      then:
        - lvgl.arc.update:
            id: eigenverbrauch_arc
            value: !lambda return x;
        - lvgl.label.update:
            id: eigenverbrauch_label_value
            text:
              format: "%0.0f %%"
              args: [id(eigenverbrauch).state]

globals:
  - id: battery_is_charging
    type: bool
    restore_value: no
    initial_value: 'false'

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

light:
  - platform: neopixelbus
    id: side_lights
    pin: GPIO25
    variant: SK6812
    num_leds: 10
    type: GRB
    name: "Side Lights"
#    restore_mode: RESTORE_DEFAULT_OFF
    initial_state:
      brightness: 50%
    default_transition_length: 0ms

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
  - file:
      type: gfonts
      family: Roboto
      weight: 900
    id: roboto_large
    size: 28
    bpp: 4
  - file:
      type: gfonts
      family: Roboto
      weight: 900
    id: roboto
    size: 16
    bpp: 4
  - file:
      type: gfonts
      family: Roboto
      weight: 200
    id: roboto_small
    size: 14
  - file: "fonts/materialdesignicons-webfont.ttf"
    id: battery_icons_20
    size: 20
    bpp: 4
    glyphs: [
      "\U000F007A", # mdi-battery-10
      "\U000F007B", # mdi-battery-20
      "\U000F007C", # mdi-battery-30
      "\U000F007D", # mdi-battery-40
      "\U000F007E", # mdi-battery-50
      "\U000F007F", # mdi-battery-60
      "\U000F0080", # mdi-battery-70
      "\U000F0081", # mdi-battery-80
      "\U000F0082", # mdi-battery-90
      "\U000F0079", # mdi-battery (full)
      "\U000F008E", # mdi-battery-outline
      "\U000F0091", # mdi-battery-unknown
      "\U000F0084", # mdi-battery-charging
      ]


lvgl:
  pages:
    - id: main_page # Default page
      width: 320
      height: 240
      pad_top: 25
      bg_color: 0xFFFFFF
      border_opa: TRANSP
      scrollbar_mode: "OFF"
      layout:
        type: FLEX
        flex_flow: ROW_WRAP
        flex_align_main: SPACE_EVENLY
        flex_align_track: START
        pad_row: 0
      widgets:
        - arc:
            id: battery_arc
            align: CENTER
            arc_rounded: false
            width: 105
            height: 105
            pad_all: 0
            arc_width: 14
            indicator:
              arc_rounded: false
              arc_width: 14
            widgets:
              - label:
                  id: battery_label
                  align: CENTER
                  text_align: CENTER
                  y: -8
                  text: "LVL"
                  text_font: roboto
              - label:
                  id: battery_label_value
                  width: 80
                  text_align: CENTER
                  text: "BATT"
                  align_to:
                    id: battery_label
                    align: OUT_BOTTOM_MID

        - arc:
            id: autarkie_arc
            arc_rounded: false
            width: 105
            height: 105
            pad_all: 0
            indicator:
              arc_rounded: false
              arc_color: dimgrey
              arc_width: 14
            arc_width: 14
            widgets:
              - label:
                  id: autarkie_label
                  align: CENTER
                  text_align: CENTER
                  y: -8
                  text: "AUT"
                  text_font: roboto
              - label:
                  id: autarkie_label_value
                  width: 80
                  text_align: CENTER
                  text: "AUT"
                  align_to:
                    id: autarkie_label
                    align: OUT_BOTTOM_MID

        - obj:
            height: 105
            width: 105
            align: CENTER
            border_width: 0
            pad_all: 0
            widgets:
              - meter:
                  height: 100%
                  width: 100%
                  border_width: 0
                  align: CENTER
                  scales:
                    - range_from: -10
                      range_to: 10
                      angle_range: 270 # sets the total angle to 180 = starts mid left and ends mid right
                      ticks:
                        count: 0
                      indicators:
                        - line:
                            id: bilanz_value
                            width: 4
                            r_mod: 12 # sets line length by this much difference from the scale default radius
                            value: -2
                        - arc: # first half of the scale background
                            color: 0xFF3000
                            r_mod: 10 # radius difference from the scale default radius
                            width: 14
                            start_value: -10
                            end_value: 0
                        - arc: # second half of the scale background
                            color: 0x00FF00
                            r_mod: 10
                            width: 14
                            start_value: 0
                            end_value: 10
              - obj: # to cover the middle part of meter indicator line
                  height: 68
                  width: 68
                  radius: 34
                  align: CENTER
                  border_width: 0
                  pad_all: 0
              - label: 
                  id: bilanz_label
                  align: CENTER
                  text_align: CENTER
                  y: -8
                  text_font: roboto
                  text: "BLZ"
              - label: 
                  id: bilanz_label_value
                  width: 80
                  text_align: CENTER
                  text: "0"
                  align_to:
                    id: bilanz_label
                    align: OUT_BOTTOM_MID

        - arc:
            id: eigenverbrauch_arc
            arc_rounded: false
            width: 105
            height: 105
            pad_all: 0
            indicator:
              arc_rounded: false
              arc_color: dimgrey
              arc_width: 14
            arc_width: 14
            widgets:
              - label:
                  id: eigenverbrauch_label
                  align: CENTER
                  text_align: CENTER
                  y: -8
                  text: "PVEV"
                  text_font: roboto
              - label:
                  id: eigenverbrauch_label_value
                  width: 80
                  text_align: CENTER
                  text: "PVEV"
                  align_to:
                    id: eigenverbrauch_label
                    align: OUT_BOTTOM_MID
```