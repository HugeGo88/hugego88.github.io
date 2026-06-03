+++
date = '2026-06-02'
draft = false
tags = ['Home Assistant', 'EVCC', 'Volkswagen', 'API', 'OBD2', 'GL.iNET', 'MeatPi', 'Wireguard', 'FRITZ!Box']
title = 'Volkswagen dreht die API ab, dann brauche ich das Abo auch nichtmehr ...'
categories = ['Blog']
+++

Seit nun 2 Jahren bin ich Besitzer eines e-Golf. Einer der ersten Dinge nachdem ich den Golf gekauft habe war es mir das Abo für die App zu holen, damit ich die Fahrzeugdaten via API auslesen kann.  

Dabei habe ich wenig die App und mehr die Integration von Home Assistant verwendet. Damit konnte ich dann auf meinem EPaper-Display den Ladestand des Akku anzeigen oder wie lange das Laden des Autos noch dauert. Auch Automatisierungen wie das Ein- und Ausschalten der Wallbox wurden so realisiert. All das geht nun nicht mehr, nicht wegen mir. Ich habe schön brav schon über 150 € in den Volkswagenkonzern versenkt, damit ich auf Daten die **mein Auto** erzeugt, auch darauf zugreifen kann. Das hat nun ein Ende da Volkswagen einfach den API Endpunkt abgedreht hat. Erst letzte Woche wollte ich mal EVCC einrichten und ausprobieren, einen Tag später tut nun nichts mehr.  
Schon Lange hatte ich die Idee mich von Volkswagen zu lösen, nicht nur, dass ich das Geld nicht einwerfen wollte, ich war auch mit den Echtzeitdaten meines Golf nicht zufrieden und da in diesem Golf ein 2G Modul eingebaut ist, war es auch nur eine Frage der Zeit, bis ich nichtmehr auf die Daten zukomme. Dass ich erst im April nochmal Geld eingeworfen habe, tut nun richtig weh, aber dafür ist die Motivation nun da sich unabhängig zu machen von VW.

## Die Idee

Die aktuellen Daten von einem Auto auszulesen ist heute tatsächlich kein Hexenwerk mehr, es gibt eine vielzahl von Donglen die nicht anderes tun. Meist braucht man hierfür dann noch eine App damit sich der Dongle dann via Bluetooth mit der dazugehörigen App verbinden kann. Das ist für mich natürlich nicht der Anwendungsfall, sollen die Daten doch in Home Assistant.  
Deshalb habe ich mir mal den [MeatPi](https://www.meatpi.com) angeschaut, dieser kann über WLAN die Daten senden, als Protokoll verwendet er dabei MQTT, das ist klasse, habe ich doch einen Broker am Laufen der nur darauf wartet, dass Daten an ihn geschoben werden. Das alleine ist aber noch nicht ganz das was ich will. Ich würde gerne die Daten von meinem Auto auch haben wenn ich gerade unterwegs bin. Sprich ich will auch wenn ich unterwegs bin sehe wie weit ich noch fahren kann oder wie lange das Laden noch dauert. Deshalb brauche ich noch eine weitere Komponente.  
Diese weitere Komponente soll ein [GL.iNet Mobile Router](https://www.amazon.de/GL-iNet-GL-XE300-Industrial-T-Mobile-Extender/dp/B08R7H75S4/ref=sr_1_4?__mk_de_DE=ÅMÅŽÕÑ&crid=3B571XNNN8DYZ&dib=eyJ2IjoiMSJ9.WUJzsBP8gWJOozkdvLmltYKFnHwRKeZcurPrPHMJoK9-P-NM_PDDzmxQritjP_4mnAYY7Rrcfc9_njFBICWRS_Wv0mkCIjULYHy6kKLUlhDve-D9wFQIybbSeOkC8laxZU7z-DnYcxJn6EKyxwC_1L_z840MAaW-wYWL6JILEyry3kY609Z0TrWnUjli6sO9MwIUttYcIFXuut261P0RGSIeWuIAaM5vL40jmvxtWn0.0XItAiCK8oaKFnFgG459OG5vJ6OIVKbbbLHnlN75yQ0&dib_tag=se&keywords=gl.inet%2B4g&qid=1780433085&sprefix=gl.inet%2B4g%2Caps%2C159&sr=8-4&th=1) werden. Dieser kann dann ein Netzwerk aufmachen auf das sich der MeatPi verbindet. Da ich auf meiner FRITZ!Box keine Ports freigegeben habe, muss ich damit die Daten dann auch an meinen Broker kommen via WireGuard mich mit meinem Heimnetz verbinden. Wenn ich de Funktionen des GL.iNet Router korrekt verstehe kann dieser genau das. Anstelle also 5€ pro Monat an VW zu senden brauche ich eine 5€ Zusatz Sim Karte, die ich dann aber auch für alle Geräte im Auto verwenden kann.  

## Vor- und Nachteile dieser Lösung 

Vorteile
- Kann für jedes Auto verwendet werden
- Unabhängig von VWs Lust und Laune

Nachteile
- Steuerung nicht möglich
- Hardwareanschaffungskosten

## Umsetzung ASAP

Jetzt geht es ersteinmal in den Urlaub, aber danach wird das Thema das ich schon lange vor der Brust habe endlich mal angegangen. Weg von der Cloud, weg von der Willkür von OEMs die den Hals nicht voll genug bekommen und nicht nur meine Daten wollen, sondern diese mir dann nichtmal zur Verfügung stellen und das obwohl ich hierfür auch noch Geld eingeworfen habe. Für mich steht fest, VW sieht von mir für diesen Dienst kein Geld mehr.

## Auch noch interessant

Der Hersteller des MeatPi hat auch ein Gerät in der Pipeline das die Arbeit des GL.iNET Routers übernehmen kann. Zusätzlich mit GPS [ESPNetLink](https://www.crowdsupply.com/meatpi-electronics/espnetlink). 