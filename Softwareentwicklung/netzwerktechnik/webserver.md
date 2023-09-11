---
sort: 2
---

# Installation Webserver Apache

## Benötigte Pakete:
- apache2

Liste der Installationspakete aktualisieren und Webserver-Pakete installieren:

(Allen Befehlen zur Systemadministration ```sudo``` voranstellen!)

```apt update && apt install -y apache2```
## Startpunkt im Document Root: 
```/var/www/html/index.html```

## Dienst start, stop, restart... mittels systemd
``` systemctl start | stop | restart | enable | disable apache2```

## Persönliche Webseiten 
- Modul aktivieren: ```a2enmod userdir```
- Webserver neu starten: ``` systemctl restart apache2```
- Daten im home-Verzeichnis: ```/home/pi/public_html/index.html```

