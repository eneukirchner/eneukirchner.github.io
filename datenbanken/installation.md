---
sort: 2
---

# Installation LAMP-Stack
## apache + PHP

Installation von Programmpaketen allgemein. 

- Programmliste aktualisieren: `sudo apt update`
- Programm installieren: `sudo apt install programmname -y`

Installiere folgende Pakete:

- __apache2__
- __libapache2-mod-php__

Mit der Eingabe der URL des Hosts sollte die Testseite im Browser erscheinen. Wo liegt das dazugehörige __index.html__ ? _Tipp: die Konfigurationsfiles des Webservers sind unter __/etc/apache2__  zu finden. Suche mit dem Kommando __grep__ (Hilfe `man grep`) nach dem Schlüsselwort __DocumentRoot__._

Editiere __index.html__, sodass der Rechnername angezeigt wird. Erstelle ein neues File __test.php__ und überprüfe damit die Funktion des PHP - Moduls.
```
<?php
  phpinfo();
?>
```
### Webverzeichnisse für jeden einzelnen Benutzer
Erweitere die Konfiguration, sodass jeder User seine eigene Seite z.B. in __/home/pi/public_html__ erstellen kann, die dann im Browser unter <http://familienname.local/~pi>
erscheint: `sudo a2enmod userdir` Nach jeder Konfigurationsänderung muss der Webserver neu gestartet werden.

Mit dem Programm __systemd__ können bei neueren Linux-Distributionen Serverdienste gesteuert und überwacht werden. Schlage in den Manpages (z.B. `man systemctl`) nach, um die passende Kommandosyntax zu finden.

- __systemctl__: Starten / Stoppen / Neustarten von Serverdiensten
- __journalctl__: Lesen der Logfiles

Editiere die Datei __/etc/apache2/mods-enabled/php*.conf__ wie im Kommentar am Ende angegeben, um auch in den Benutzerverzeichnissen PHP zu aktivieren.

In weiterer Folge soll nach Möglichkeit immer nur unter __public_html__ im eigenen home gearbeitet werden (z.B. als user pi).

## Installation von MySQL / Mariadb + zugehörige PHP-Module 
### Benötigt werden:

- __mariadb-server__
- __php-mysql__
- __mycli__ (optional: etwas komfortabler als das Standard-Textinterface mysql)
- __adminer__ (optional: Webinterface zur Administration)

Nach der Installation startet der MySQL-Server automatisch.

### Das Anlegen von Datenbanken:

`sudo mysql -u root -p`

Dann mit Eingabetaste ohne Passwort in die MySQL-Kommandozeile!

Zur "normalen" Arbeit mit der unter root erstellten Datenbank einen eigenen MySQL-Benutzer (z.B. pi, fred, ...) verwenden. Einloggen geht dann entweder mit

`mysql -u fred -p`

oder komfortabler (optional) mit

`mycli -u fred`

## Ausgabe einer zuvor erstellten Tabelle über eine Webseite

Wir verwenden zum Zugriff auf MySQL via PHP einen objektorientierten Ansatz - derzeit die programmtechnisch übersichtlichste und sicherste Methode.

Doku + Beispiele (jeweils am Ende jedes Eintrages):

<http://php.net/manual/en/book.pdo.php>
