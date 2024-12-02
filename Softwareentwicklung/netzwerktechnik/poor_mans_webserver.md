---
sort: 3
---

# Poor Man's Webserver

Um einen Webserver öffentlich im Internet zugänglich zu machen, werden grundsätzlich zwei für gewöhnlich kostenpflichtige Voraussetzungen benötigt:
- eine öffentliche IP-Adresse
- ein Hostname mit offizieller Domain

Für lediglich privat genützte Websites, beispielsweise zur Home-Automatisierung oder als Nextcloud-Instanz, gibt es Wege, ohne weitere Investitionen einen von außen erreichbaren Server einzurichten.

## Öffentliche IP-Adresse
Zwei mögliche Szenarien:
- Der Provider stellt eine öffentliche IPv4-Adresse zur Verfügung, die am Router direkt nutzbar ist (Beispiel: Magenta Kabelanschluss). In diesem Fall müssen an der Router-Firewall einlangende Pakete auf Port 80/http und 443/https durchgelassen werden. Zusätzlich muss eine Weiterleitung dieser Ports auf den im eigenen Intranet laufenden Webserver eingerichtet werden.
- Der Provider stellt IPv6-Adressen zur Verfügung. Diese sind per Default öffentlich, daher muss nur die Firewall den einlangenden Traffic zulassen; die Portweiterleitung erübrigt sich. Einfache mobile WLAN-Router mit SIM-Karte haben meist keine Firewall, dort muss dann gar nichts konfiguriert werden.

## Hostname
IP-Adressen für Privatkunden sind praktisch immer dynamisch, sie werden also in regelmäßigen Abständen neu vergeben. Um eine permanent erreichbare URL für den Webserver einzurichten, können Dyndns-Services verwendet werden. Dabei läuft auf dem Server ein Client, der seine aktuelle Adresse periodisch an den Dyndns-Anbieter sendet. Die meisten dieser Services sind gratis, es ist nur eine Registrierung notwendig. Die Auswahl an Domainnamen ist allerdings recht eingeschränkt.

### Installation des Dyndns-Clients am Webserver
Paket installieren: `apt install ddclient` Beim Installieren öffnet sich ein Konfigurations-Dialog; hier einfach alle Punkte mit Enter durchgehen, ohne weitere Eingaben.

Konfigurieren (Beispiel für IPv6 mit [dynv6.com](https://dynv6.com)): `/etc/ddclient.conf`

*Die auf der Seite von dynv6 unter "Installation instructions" gelieferte Konfiguration bezieht sich auf eine ältere Version von ddclient und funktioniert mit der aktuellen (unter Debian 12) nur eingeschränkt!*

```
ssl=yes
usev6=ifv6, ifv6=wlan0
usev4=disabled
protocol=dyndns2 
server=ipv6.dynv6.com 
login=none 
password='**************' 
mein-super-server.dynv6.net

```
Unter `password=` kommt das nach Einrichtung beim Dyndns-Anbieter generierte Token hinein.

Zum Abschluss den Client starten und  so einrichten, dass er bei jedem Systemstart automatisch hochfährt. 
```
systemctl restart ddclient
systemctl enable ddclient
```

Im Webinterface des Dyndns-Anbieters sollte dann zum gewählten Hostnamen eine gültige Adresse zeigen. Damit ist der Server von außen erreichbar.

## Absicherung
- Installation einer Firewall, wobei nur Web- und SSH-Dienste (zur Wartung) durchgelassen werden:
  
```
apt install ufw

ufw allow ssh
ufw allow http
ufw allow https

ufw enable
ufw status
```
- Absicherung gegen Brute-Force-Attacken: `apt install fail2ban`

- Verschlüsselte Website einrichten (https):
  Kostenlose und offiziell anerkannte Webserver-Zertifikate bietet das Projekt [letsencrypt](https://letsencrypt.org/) an. 
  - Installation: `apt install certbot`
  - Starten der Konfiguration mit `certbot --apache` (falls dieser Webserver zum Einsatz kommt)
  - Neustart des Webservers: `systemctl restart apache2`




