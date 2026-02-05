+++
date = '2026-03-31'
draft = true
tags = ['Home Assistant', 'Zendure', 'AIO2400', 'Balkonkraftwerk', 'MQTT', 'Speicher']
title = 'Zendure Balkonkraftwerkspeicher auf lokalen MQTT Broker umbiegen mit Nulleinspeisung in Home Assistant'
categories = ['Blog']
+++

## Einleitung

Auf dem Weg zum eigenen Smart Home stößt man immer wieder auf Cloud Lösungen. Lösungen die nicht selten irgendwann abgeschaltet werden und damit auch die gekauften Geräte unbrauchbar machen oder wie es im englischen heißt bricken. Aus dem teuren technischem Gerät wird also auf einmal ein nutzloser Ziegelstein (Brick). Doch wie verhindert man nun dieses Bricken der Geräte? Am besten kauft man sich natürlich nichts, dass in irgendeiner Form einen Cloud zwang hat. (Wenn es nach mir ginge, müsste die EU da mal ein wenig Druck machen, zu jedem Gerät, dass eine IoT Funktion hat muss es zu dem proprietären Anschluss auch einen offenen Zugang geben, sei es MQTT, REST, GraphQL oder meinetwegen Modbus) Leider gibt es manchmal Geräte die man aber haben will, trotz dem Cloudzwang. Zum Glück gibt es immer wieder Leute die Systeme reverseengineeren und das dann auch zur freien Verfügung stellen. So auch geschehen bei den Zendure Speicher Systemen. 

Ich selber besitze zwei AIO 2400 von Zendure und bin bevor ich mir diese besorgt habe zum Glück über dieses Git gestoßen. [Solarflow BT Manager](https://github.com/reinhard-brandstaedter/solarflow-bt-manager). Reinhard Brandstäter hat hier die Bluetooth Kommunikation beim Einrichten der Speicher reverse Engineered. Hier kann man nun entweder die aktuellen Daten über Bluetooth auslesen, oder gleich die Konfiguration so ändern, dass die Daten nichtmehr an die China-Cloud gesendet werden sondern an einen lokalen MQTT Broker. 

## Konfiguration

Die Konfiguration ist an sich richtig gut beschrieben, weshalb ich hier auf eine detaillierte Beschreibung verzichte. Aber hier mal ein paar meiner Erfahrungen mit diesem Repo. 

Ich habe anfangs nur die Daten via Bluetooth abgeholt, also expliziert keine Umkonfiguration vorgenommen. Dies habe ich mithilfe eines Raspberry Pi 4 gemacht. Dort habe ich das Skript einfach mit diesem Befehlt gestartet ```python3 solarflow-bt-manager.py -i -b 192.168.1.```. Zur Sicherheit habe ich mein Skript via pm2 einfach alle paar Minuten neugestartet. Sicher ist sicher. Eine Anleitung wie man pm2 auf einem PI einrichtet gibt es zum Glück schon hier [PM2 Einrichtung](https://docs.magicmirror.builders/configuration/autostart.html). Einmal am Tag habe ich dann noch die Schaltbare Steckdose neugestartet welche meinen PI mit Strom versorgt hat. Abgesehen davon, dass ich immer wieder Datenausfälle hatte, hatte es mich auch gewurmt, dass ich extra dafür einen Raspi habe laufen lassen müssen. Also wurde es Zeit mal das Umkonfigurieren der Speicher zu Probieren.

Beim Umkonfigurieren wird dem Speicher die Adresse eines neuen MQTT-Broker zugewiesen. Wichtig hierbei, das Passwort und ein Nutzer sind im Skript zwar vorgesehen, aber leider wird das nicht vom Speicher übernommen. Nach langem rumprobieren war mir klar, ich brauche einen Broker der auch ohne Authentifizierung funktioniert, der typische Mosquitto broker, welcher von vielen als Home Assistant Addon verwendet wird, kann das leider nicht. Meine Lösung war deshalb einen MQTT-Broker zu verwenden welcher auch ohne Authentifizierung funktioniert, weshalb ich mich für den EMQX Broker entschieden habe. Einen Eintrag wie man den Broker einrichtet habe ich [hier](https://www.tausch.one/posts/2025/11-01-emqx-in-home-assistant/) hinterlegt. 

```python
WIFI_PWD = os.environ.get('WIFI_PWD', '12341234')
WIFI_SSID = os.environ.get('WIFI_SSID', 'FRITZ!Box 1234 Cable WQ')
SF_DEVICE_ID = os.environ.get('SF_DEVICE_ID', 'abcd1234')
SF_PRODUCT_ID = os.environ.get('SF_PRODUCT_ID', 'yWF7hV')
GLOBAL_INFO_POLLING_INTERVAL = os.environ.get('GLOBAL_INFO_POLLING_INTERVAL', 60)
mqtt_user = os.environ.get('MQTT_USER', 'MQTT_USER')
mqtt_pwd = os.environ.get('MQTT_PWD', 'MQTT_PWD')
```

```python3 solarflow-bt-manager.py -d -w 'FRITZ!Box 6590 Cable WQ' -b 192.168.178.77```

## MQTT einrichten in Home Assistant

[MQTT Mapping](https://github.com/Schwippser/Solarflow-mqtt-HA)

[Github YAML](https://github.com/z-master42/solarflow/tree/main?tab=readme-ov-file). 


[Erweiterung MQTT Mapping](https://community.simon42.com/t/anleitung-zendure-solarflow-devices-aus-der-zendure-cloud-befreien-mit-ha-daten-lokal-abfragen/36861/61)

## Automatisches Laden aller Werte