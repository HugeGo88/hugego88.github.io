+++
date = '2025-10-25'
draft = false
tags = ['Home Assistant', 'Anleitung', 'OpenEpaperLink', 'HA YAML Designer', 'Epaper', 'Price tag']
title = 'OpenEpaperLink HA YAML Designer'
categories = ['Blog']
+++

![Price Tags](featured.jpeg "Beispiel für Price Tags, bespielt über Home Assistant")

## Einleitung

Immer mehr Supermärkte haben mittlerweile Epaper Pricetags in den Regalen hängen. Nicht nur, dass man diese Preisschilder mehr und mehr sieht, nein sie werden auch größer, gefühlt scharfer und auch Farben können mittlerweile gut dargestellt werden. 

Ich finde diese Displays einfach faszinierend. Sie brauchen nur beim Bildupdate Strom und können somit mit ein paar kleinen Knopfzellen betrieben werden und das über Wochen und Monaten, ohne eine Batterie tauschen zu müssen. 

Gut, dass ich nicht der einzige bin der diese Displays super findet. Denn sonst gäbe es das tolle Projekt [OpenEpaperLink](https://openepaperlink.de) nicht. Wenn ihr an alte Prizetags ran kommt oder einfach mal die Augen auf Ebay oder Kleinanzeigen offen haltet kann man für kleines Geld an gebrauchte Tags ran kommen. Wer sich das Flashen der Tags sparen will, hält gleich nach fertig geflaschten Tags Ausschau. Gleiches gilt für den dazugehörigen Access Point, den kann man entweder den AP selber zusammen bauen, oder auch hier gibt es fertige APs die man online kaufen kann. 

Jeder der sich jetzt frage wie man diese Tags den nun bespielen kann, für den habe ich gute Nachrichten. Denn wer schon ein Home Assistant am Laufen hat, der hat die halbe Miete schon. Es gibt eine Integration die man über HACS installieren kann [LINK](https://github.com/OpenEPaperLink/Home_Assistant_Integration). Hier kann man über Automationen die Tags bespielen, doch wo und wie erstelle ich das Design für diese Tags? Hier kommt das heutige Tool [HA YAML Designer](https://atc1441.github.io/oepl_yaml_designer/) in spiel.

## Design Einführung

Geht man auf die [Seite](https://atc1441.github.io/oepl_yaml_designer/) geht der Designer mit einer Toolbox auf der linken Seite. Hier drinnen sind 16 Bausteine welche man zum Erstellen des Designs verwenden kann. Von simplen Textbausteinen, bis hin zu QR Code Generator oder sogar Graphen. Auch wenn ich als Programmierer kein großer fan von grafischen Editoren bin, muss ich zugeben, es ist einfach schön wenn man sich nicht in jede Syntax eindenken muss.

![Designer](images/overview.png "Startseite des Designers")

Wer aber erst noch ein wenig Ideen für seine Designs braucht, der hat Glück. Der Entwickler hat sich die Mühe gemacht und ein paar Beispiele designed. 

![Beispiele](images/example.png "Beispiel Auswahl")

Bevor man aber das Design erstellt, sollte man erste einmal die Größeneinstellungen der Tags anpassen. Das macht man unten links im Eck. Ist das geschehen kann man sich die einzelnen Elemente seines Designs zurecht ziehen. Wichtig hierfür, damit die Daten von Home Assistant später ausgegeben werden können, muss die Entität angegeben von welcher die Werte stammen sollten. 

![Entität](images/homeassistant.png "Beispiel für die Anbindung an Home Assistant")

Die Entitäten werden dabei als Templates übergeben. Unten wird die Entität meines eGolfs ausgelesen. Das entsprechende Feld hierfür ist das 'Value (Text or HA Template)'

```
{{states('sensor.egolf_battery_cruising_range')}} km
```

## Einbindung in Home Assistant

Zuerst testen wir unser neues Layout mit Hilfe der Entwicklerwerkzeuge. Nicht vergessen, davor die OpenEPaperlink Integration installieren. 

![Test](images/home_assistant_test.png "Testen des Layout in den Entwicklerwerkzeugen")

Hier kann jetzt "Benutzerdefiniertes Bild zeichnen" ausgewählt werden. Als Ziel geben wir einen der in OpenEPaperlink konfigurierten Tags an. Anschließend kann das Layout, welches wir vorher definiert haben in den Payload kopiert werden. Mit "Aktion ausführen" wird nun das Bild erstellt und über den Access Point an den Tag gesendet. Wie immer kann das eine Weile dauern, da die Tags, je nach Konfiguration" sich nur alle paar Minuten am AP anmelden. 

Funktioniert das Zeichnen der Bilder, so kann mit den gleichen Schritten eine Automatisierung erstellt werden. 

## Hardware

### EPaper Tag

Meine Tags sind Solu M vom Type ST - QR26000. Diese Tags haben eine Auflösung von 296 x 152 Pixeln und können Schwarz, Weiß und Rot darstellen. Gekauft habe diese Tags über Ebay. Die Tags waren bereits mit OpenEPaperLink geflashed. 

![Price Tags](images/pricetag.jpeg "Rückseite des Price Tags")

Ausgeliefert wurden sie im Deepsleep Modus. Was bedeutet, dass die Tags keine Daten empfangen. Erst wenn die Tags resetet wurden, können sie wieder Daten empfangen. 

![Tag im Deep Sleep](images/deepsleep.jpeg "Hier das Tag Display. Mit dem Hinweis, dass der Tag im Deep Sleep ist und wie man in Reseten kann")

Hier sieht man das Display im Deepsleep Modus. Netterweise sieht man gleich die Anleitung um diesen Tag wieder aufzuwachen. Hierfür muss man die Knopfzellen raus nehmen und dann eine der Knopfzelle verkehrt herum einlegen. Dies schließt den Stromkreis kurz und versetzt den Tag in den Reset. Werden die Batterien nun wieder eingesetzt Bootet der Stick neu. 

![Leerer Tag](images/innen1.jpeg "Hier der Leere Tag. Gut zu sehen sind die Pins für die Stromversorgung und auch die Pad über welche der Tag mit einer Custom Firmware geflashed werden kann")

![Zellen in Tag](images/innen2.jpeg "Hier der Tag mit den eingelegten Knopfzellen.")

Ist der Tag wieder aufgeweckt sucht dieser gleich nach einem Access Point und wenn wieder gefunden wurde wird auf die Daten des selbigen gewartet. 

![Waiting for Data](images/waiting.jpeg "Hier sieht man einen Tag der am AP angemeldet ist und auf Daten wartet. ")

### Access Point

Auch den Access Point habe ich mir zuschicken lassen. Ich hatte das Glück, dass ich jemand gefunden habe, der diese AP für einen zusammenbaut und bespielt. Eine Anleitung findet sich aber auch auf der Projektseite. Hier gibt es verschiedene Bauanleitungen. [Projekt Seite](https://openepaperlink.de/aps)

![Access point](images/ap.jpeg "Access Point für OpenEPaperLink")

PS: Ja ich hätte den AP und die Tags selber flashen können und ja ich hatte den AP vermutlich auch selber bauen können, aber ich die Zeit habe ich leider nicht mehr. 

## Beispiel gesamtes Layout 

```
- type: icon
  value: "mdi:car"
  x: 0
  y: 0
  size: 29
  fill: black

- type: text
  value: "Reichweite"
  x: 41
  y: 8
  size: 15

- type: text
  value: "Akku"
  x: 41
  y: 29
  size: 15

- type: text
  value: "{{states('sensor.egolf_battery_cruising_range')}} km"
  x: 131
  y: 8
  size: 15
  color: red

- type: text
  value: "{{states('sensor.egolf_battery_level')}} %"
  x: 131
  y: 29
  size: 15
  color: red

- type: plot
  data:
  - entity: sensor.sf_hub2000_battery_level
    color: red
    width: 2
  ylegend:
    width: 18
    size: 9
    color: black
  yaxis:
    tick_every: 20
    color: grey
  xlegend:
    width: 18
    size: 9
    color: black
  xaxis:
    tick_every: 6
    color: grey
  x_start: 0
  y_start: 44
  x_end: 296
  y_end: 152
  duration: 86400
  font: ppb.ttf
```