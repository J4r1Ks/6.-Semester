Tags: [[Verteilte Systeme]], [[Verteilte Systeme Praktikum]]

## Aufgabe 1


![[Screenshot 2025-03-11 164759.png]]

### 1.

| **Indikator**                                               | **Bewertung**                                                                                                   |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| (A1) gemeinsame Nutzung von Ressourcen                      | Git wird von verschiedenen Entwicklern verwendet. Diese haben unterschiedliche Systeme.                         |
| (A2) anbieten von offenen Schnittstellen                    | Es ist sinnvoll, Schnittstellen anzubieten, um in bestehende Projekte integriert zu werden.                     |
| (A3) parallele / nebenläufige Ausführung von Aktivitäten    | Entwickler können voneinander unabhängig Änderungen am System unternehmen ohne  sich gegenseitig zu blockieren. |
| (A4) dynamische Skalierung des Rechenbedarfs                | Lasten können verteilt werden, da Zugriffe nicht über eine zentrale Instanz stattfinden.                        |
| (A5) erhöhte Fehlertoleranz                                 | Jeder Nutzer hat ein vollständiges Backup, um einen totalen Verlust der Daten zu verhindern.                    |
| (N1) höhere Entwicklungskosten                              | Ist komplexer als eine zentrale Lösung, aber der Aufwand lohnt sich.                                            |
| (N2) gefordertes Sicherheitsniveau ist schwerer zu erfüllen | Sensible Daten können auf mehreren Geräten gespeichert werden, was das Sicherheitsrisiko erhöht.                |
| (N3) höhere Wartungsaufwände                                | Eine korrekte Versionsverwaltung ist schwierig aber notwendig um Konflikte zwischen Versionen zu verhindern.    |
| (N4) Verhalten nicht vorhersehbar                           | Da jeder Entwickler lokal arbeiten kann, kann es zu Merge-Konfliken kommen.                                     |

### 2.

| **Indikator**                                               | **Anwendung**                                                                                                                                      |
| ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| (A1) gemeinsame Nutzung von Ressourcen                      | Mehrere Nutzer können auf eine Datei zugreifen und verändern.                                                                                      |
| (A2) anbieten von offenen Schnittstellen                    | Viele Cloud-Speicher bieten APIs zur Integration in andere Anwendungen.                                                                            |
| (A3) parallele / nebenläufige Ausführung von Aktivitäten    | Mehrere Nutzer können gleichzeitig arbeiten, Änderungen werden synchronisiert.                                                                     |
| (A4) dynamische Skalierung des Rechenbedarfs                | Der Speicherbedarf wächst flexibel mit der Anzahl der Nutzer und Dateien.                                                                          |
| (A5) erhöhte Fehlertoleranz                                 | Daten sind redundant über mehrere Server verteilt, sodass ein Serverausfall keine Datenverluste verursacht.                                        |
| N1) höhere Entwicklungskosten                               | Eine verteilte Architektur würde die Entwicklung und Wartung stark erschweren.                                                                     |
| (N2) gefordertes Sicherheitsniveau ist schwerer zu erfüllen | Finanztransaktionen erfordern höchste Sicherheit, eine verteilte Speicherung könnte das Risiko von Datenlecks oder Manipulationen erhöhen.         |
| (N3) höhere Wartungsaufwände                                | Inkonsistenzen in verteilten Datenbanken könnten zu schwerwiegenden Problemen führen (z. B. doppelte oder fehlgeschlagene Transaktionen).          |
| (N4) Verhalten nicht vorhersehbar                           | Verzögerungen oder Inkonsistenzen in einem verteilten System könnten dazu führen, dass Kontostände oder Transaktionen fehlerhaft angezeigt werden. |

## Aufgabe 2

![[Screenshot 2025-03-11 122640.png]]

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

### 2)
1. NTP (Network Time Protocol):
	- Port: UDP 123
	- Funktion: Synchronisiert die Systemzeit von Computern über Netzwerke hinweg.
2. HTTPS (Hypertext Transfer Protocol Secure):
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

## Aufgabe 3

### 2)
`./daytime 131.173.110.43`
`telnet localhost 9013`

### 3)
`telnet 131.173.110.42 9013` -> Uhrzeit angezeigt

### 4)
`telnet time.nist.gov 13` -> 60751 25-03-25 11:45:42 50 0 0 607.5 UTC(NIST) *
`telnet time-a.nist.gov 13` -> 60751 25-03-17 11:59:57 50 0 0 513.2 UTC(NIST) *

### 5)
#### Lokale Umgebung:
+ Latenzen sind sehr gering. Zeitstempel ist nahezu exakt.

#### Externe Server:
+ Netzwerklatenzen und Verarbeitungszeiten können signifikant groß sein. Das bedeutet, dass man die Zeitunterschiede beachten sollte.

#### Fazit:
+ Bei Aufgabe 3. greifen wir auf ein lokales Netz zu, während bei Aufgabe 4 diese Anfrage auf ein externen Server passiert. Bei 3 muss man keine Latenzen berücksichtigen aber bei 4. 