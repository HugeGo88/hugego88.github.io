+++
date = '2025-05-30T09:10:33-07:00'
draft = false
tags =['Samrt Home', 'Homematic', 'Home Assistant']
categories = ['Youtube']
title = 'Anleitung: Homematic HM-DIS WM55 Display in Home Assistant mit Python-Skripten steuern'
+++

## Zusammenfassung

Das Homematic HM-DIS WM55 Status-Display ist ein nützliches Gerät, dessen volle Funktionalität in Home Assistant (HA) jedoch nicht von Haus aus gegeben ist. Dieser Beitrag fasst die technische Lösung zusammen, um das Display über die HA-Homematic-Integration mit dynamischen Inhalten wie Zuständen und Temperaturen zu versorgen.

Die technische Lösung basiert auf Python-Skripten:

1. Vorbereitung in Home Assistant:

   - Aktivieren Sie die python_script-Erweiterung in Ihrer configuration.yaml, um eigene Skripte ausführen zu können.

1. Skript-Logik:

   - Die Lösung nutzt zwei Haupt-Python-Skripte. Das erste (Setup-spezifisch) holt aktuelle Zustandsdaten aus HA (z.B. offene Fenster, Licht-Status, Außentemperatur). In diesem Skript muss die Seriennummer des HM-DIS WM55 Displays zur eindeutigen Adressierung hinterlegt werden.

   - Die Zustände werden verarbeitet, um entsprechende Texte, Icons und Farben (Rot/Weiß) zuzuweisen.

   - Die gesammelten Daten werden als JSON-Objekt (DisplayData) aufbereitet und an das zweite Skript, UpdateDisplay.py, übergeben. Dieses Skript übernimmt die komplexe Codierung der Daten in das proprietäre, vom Display erwartete String-Format.

1. Kommunikation und Auslösung:

   - Das UpdateDisplay.py-Skript nutzt den Home Assistant Service homematic.set_device_value (z.B. Homematic IP Local), um den codierten String an den Kommunikations-Channel des Homematic-Geräts zu senden und das Display zu aktualisieren.

   - Die gesamte Aktualisierungsroutine wird durch eine Home Assistant Automation gestartet, ausgelöst durch das Drücken einer der Tasten auf dem HM-DIS WM55.

{{< alert >}}
**Warning!** Die Datenübertragung muss innerhalb eines kurzen Zeitfensters erfolgen, nachdem eine Taste gedrückt wurde. Andernfalls erscheint die Fehlermeldung „Keine Daten empfangen“ auf dem Display. Beachten Sie abschließend, dass für jedes weitere Display im System ein eigenes Datensammel-Skript erstellt und gestartet werden muss.
{{< /alert >}}

## Video

{{< youtube mt4eGhz-70o >}}

## Sourcecode

[GitHub](https://github.com/HugeGo88/HomeAssistant_Youtube/tree/main/HM-Dis-WM55)