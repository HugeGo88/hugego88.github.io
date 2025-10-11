+++
date = '2025-10-11'
draft = false
tags =['Flutter', 'Wordpress']
title = 'Flutter meets Wordpress am Beispiel CVJM Walheim App'
+++

## Warum habe ich diese App geschrieben? 

Das hatte sicherlich mehrere Gründe. Unter anderem hatte ich schlicht einfach mal Lust eine App zu schreiben. Ich dachte das muss doch sicherlich ein cooles Gefühl sein zu wissen, dass man etwas geschrieben und bereitgestellt hat, dass jetzt hunderte und tausende Nutzer einfach runter laden und verwenden können. (Spoiler: Meine App Idee ist durch ihre lokale Datenlage aber nie "abgehoben" und das ist auch okay). 

Dann hatte ich in dieser Lebensphase auch einfach die Zeit dafür. Es waren noch die Covid Ausläufe, das Leben war langsamer und irgendwie musste man die Zeit ja rum bringen. Einfach so eine App schreiben kam für mich aber nicht in Frage, ich wollte schon etwas Programmieren, dass zumindest ein wenig den Anschein von etwas Sinnvollem erweckt. Gut, dass ich ein wenig vorher Vorstand vom lokalen CVJM wurde. Hier hatte ich die komplette Homepage ersteinmal auf links gedreht. Nicht, dass ich die Arbeiten des vorherigen Admin kleinreden will, aber mir hatte die Seite nicht so zugesagt und nachdem mir mitgeteilt wurde, dass die Seite eigentlich keine aktive Pflege mehr erhält, ging ich ans Werk. Es wurden online Kalender angelegt, ein Buchungssystem eingerichtet für das Vereinsheim, Newsletter und was man sonst noch mit Wordpress Addons so alles treiben kann. Immer den Spruch von Apple im Kopf "There is an app for that" dachte ich, eigentlich braucht die neue Seite nun auch eine App. Und so ging ich auf die Reise. 

## Woher und wie wollte ich die Daten abholen? 

Immer wieder dachte ich, es muss doch einen Weg geben die App mit Daten befüllen zu können. Immer wieder bin ich davon ausgegangen, dass ich hierfür einen Dienst, oder irgend einen speziellen Endpunkt brauchen würde den Wordpress so nicht liefern würde. Bei meiner Suche im WWW fand ich dann ein Repo mit einer ganz einfach Implementierung meiner Idee. Eine single Page Applikation. Nur die Posts von der Seite das wars. Keine weiteren Features. Okay, dachte ich, das ist ein guter Start, damit kann man was anfangen. 

Beim der Sichtung der Codes stellte ich fest, dass ganz schlicht und einfach die API von Wordpress verwendet wurde. Hängt man hinter die Startseite `/wp-json/` so kommt man ganz einfach an alle Endpunkte und kann sich schon mit der Basisinstallation von Wordpress die Posts ziehen. Da sollte ich aber nicht stehen bleiben. Doch eins nach dem anderen.

Ersteinmal muss gesagt werden, wer wie ich zum ersten mal in die Webentwickler Welt abtaucht (ich bin PLC Programmierer) und Safari benutzt der wird sich mit so was konfrontiert sehen. Nicht gerade schön und nicht gerade hilfreich. 

![Safari](safari.png "JSON Darstellung in Safari")

Zum Glück ist Appels Browser aber nicht der einzige auf dieser Welt ruft man zum Beispiel diesen [Endpoint](https://cvjm-walheim.de/wp-json") in Firefox auf dann bekommt man eine schöne Darstellung der einzelnen Json Daten, so dass man auch ordentlich damit arbeiten kann. Das ist sicherlich für den ein oder anderen super trivial, aber wie gesagt, das ist eben nicht meine Domäne und deshalb fand ich es an dieser Stelle einfach mal wichtig erwähnt zu haben. 

![Firefox](firefox.png "JSON Darstellung in Firefox") 


## Github

[Hier](https://github.com/HugeGo88/cjvm_app) findest du den gesamten Quellcode der App. Mit ein paar Anpassungen sollte es möglich sein die App auch an deine Wordpress-Seite anzupassen. Eigentlich wollte ich immer mal eine App schreiben die man mit wenig Konfigurationsaufwand an seine Seite anpassen kann. Leider bin ich rein zeitlich nie ganz dazu bekommen. Aber [hier](https://github.com/HugeGo88/wordpress_widget) ist mal der Grundstein dafür gelegt. 