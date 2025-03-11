Tags: [[Verteilte Systeme]], [[Verteilte Systeme Praktikum]]

![[Screenshot 2025-03-11 122640.png]]


## 1)
### 1a)
+ Linux: `ping -c 4 <hostname>`
	- `-c 4`: sendet 4 Pakete und zeigt die Antwortzeiten.
- Windows: `ping -n 4 <hostname>`
	- `-n 4`: Sendet 4 Pakete

### 1b)
- Linux: `netstat -tulnp`
	- -`t`: TCP-Ports anzeigen
	- -`u`: UDP-Ports anzeigen
	- -`l`: nur lauschende (listening) Ports anzeigen
	- -`n`: Numerische Adressen (kein DNS-Resolve)
	- -`p`: Prozess-ID und Name anzeigen
	- Alternativ mit ss (moderner netstat): `ss -tulnp`
- Windows: `netstat -ano`
	- `-a`: Alle Verbindungen anzeigen
	- `-n`: IP-Adressen numerisch anzeigen
	- `-o`: Prozess-IDs der Dienste anzeigen
	- Prozesse zugehörig zu einer PID herausfinden: `tasklist | findstr <PID>` 

### 1c)
- Linux: `arp -n`
	- Zeigt die MAC-Adressen und IP-Adressen der erreichbaren Geräte im Netzwerk.
	- Alternativ für detailliertere Adapter-Infos: `ip neigh show`
- Windows: `arp -a`
	- Listet alle bekannte MAC-Adressen und zugehörige IPs

### 1d)
- Linux:
	- IP-Adresse eines Rechners herausfinden: `host si002x-0xx-lin`
	- DNS-Name einer IP-Adresse herausfinden (Reverse Lookup): `host 131.173.110.xx`
- Windows:
	- `nslookup si002-0xx-lin`
	- `nslookup 131.173.110.xx`

## 2)
1. NTP (Network Time Protocol):
	- Port: UDP 123
	- Funktion: Synchronisiert die Systemzeit von Computern über Netzwerke hinweg.
2. HTTPS (Hyptertext Transfer Protocol Secure):
	- Port: TCP 443
	- Funktion: Sicheres Übertragungsprotokoll für Webseiten, das Daten verschlüsselt überträgt.
3. Echo:
	- Port: TCP/UDP 7
	- Funktion: Testdienst, der empfangene Daten unverändert zurücksendet; dient zur Fehlersuche
4. SSH (Secure Shell):
	- Port: TCP 22
	- Funktion: Ermöglicht sichere, verschlüsselte Verbindungen zu entfernten Rechnern für Administration und Dateiübertragung.
5. Kerberos:
	- Port: TCP/UDP 88
	- Funktion: Netzwerk-Authentifizierungsprotokoll, das sichere Anmeldungen in unsicheren Netzwerken ermöglicht.
6. rsync:
	- Port: TCP 873
	- Funktion: Werkzeug zur schnellen und effizienten Dateiübertragung und -Synchronisation zwischen Computern.

Diese Portzuweisungen entsprechen den von der Internet Assigned Numbers Authority ([IANA](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml?&page=1)) festgelegten "Well Known Ports".

Zusatzaufgabe:
Unter Windows-Betriebssystemen werden Informationen über Dienste und deren zugeordnete Ports in der Datei `C:\Windows\System32\drivers\etc\services` gespeichert. Diese Datei listet Dienste zusammen mi ihren entsprechenden Portnummern und Protokollen auf. Sie entspricht der `/etc/services`-Datei unter Linux-Systemen.