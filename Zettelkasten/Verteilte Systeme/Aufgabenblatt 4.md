
Erstellt: 16.04.2025 19:33

Tags:[[Verteilte Systeme]], [[Verteilte Systeme Praktikum]]

# Aufgabenblatt 4

![[VS_SoSe25_Blatt_04_Aufgabe.pdf]]

## Server

### Klassenvariablen
- `std::string topic;`
- `std::list<std::string> subscribers;`

### Konstruktor
- `PubSubServiceImpl() : topic("<no topic set>") {}`
Standardkonstruktor $\rightarrow$ topic wird auf `<no topic set>` gesetzt.

### subscribe()
Wenn ein Client auf den Server subscribed ist, wird er in einem string formatiert in die Liste `subscribers` eingespeichert. Zudem wird auf der Konsole des Servers dieser Subscriber ausgegeben. (ist nur für debug zwecke)

### unsubscribe()
- Suche den client in der Liste `subscribers`und lösche ihn aus der Liste
- Falls nicht gefunden, wird auf der Konsole des Servers eine Fehlermeldung ausgegeben.

### publish()
- speichere den Inhalt der Message im string format zwischen.
- falls es einen topic existiert, ist die Ausgabe auf dem server im folgenden Format: `msgText = "[" + topic + "]" + msgText;`
  `std::cout << "[publish] Message received: " << msgText << std::endl;`
- Nun wird für jeden subscriber die Nachricht versendet.

### set_topic()
- prüfe, ob `passcode` == 0815 ist
- Wenn ja, wird die klassenvariable `topic` gleich dem `topicString` vom `request` gesetzt.

# Fragen

## Frage 1: Receiver und Client sind voneinander getrennte Prozesse. Warum ist dies so?
- **Trennung von Aufgaben:**
  Der Receiver ist dafür verantwortlich, Nachrichten (via die RPC-Methode `deliver()`) asynchron entgegenzunehmen und auszugeben, während der Client die Steuerung übernimmt (Registrierung, Publizieren, Setzen von Topics).
- **Asynchrone Kommunikation:**
  Da Nachrichten empfangen und gleichzeitig gesteuert werden müssen, erlaubt die Trennung, dass der Receiver kontinuierlich auf eingehende Nachrichten reagiert, ohne durch Steuerbefehle blockiert zu werden.
- **Robustheit und Modularität:**
  Diese Trennung stellt sicher, dass selbst wenn die Steuerung fehlerhaft arbeitet, der Empfang der Nachrichten stabil weiterläuft. Zudem können Receiver auf unterschiedlichen Rechnern laufen.

## Frage 2: Handelt es sich um ein synchron oder asynchron arbeitendes System? Woran machen Sie das fest?
- **Asynchrone Arbeitsweise:**
  Das System arbeitet asynchron, weil:
	- Der Dispatcher verteilt Nachrichten per gRPC an alle registrierten Receiver verteilt, ohne auf eine sofortige Antworten zu warten.
	- Der Receiver empfängt Nachrichten, sobald sie eintreffen, und gibt diese mit lokalen Zeitstempeln aus.
	- Der Client steuert den Prozess und empfängt Rückmeldungen, während die eigentliche Nachrichtenzustellung (deliver) ungebunden läuft.
- **Indizien:**
  -Nutzung von RPCs (insbesondere im gRPC-Stil), die asynchrone Aufrufe unterstützen
  -Trennung der Prozesse (Receiver versus Client)

## Frage 3: Die Registrierung/De-Registrierung erfolgt über IP-Adressen. gRPC arbeitet mit HTTP als Transport-Protokoll und kann auch zu Aufrufen von Diensten im Internet (z.B. Cloud) genutzt werden. Welche Probleme können dabei auftreten?
- **NAT und Firewalls:**
  Oft werden Clients hinter NAT-Router eingesetzt, wodurch die öffentliche IP-Adresse von der internen abweicht. Dies kann dazu führen, dass der Dispatcher den Client nicht korrekt erreicht.
- **Dynamische IPs:**
  Wenn Clients dynamische IP-Adressen verwenden, kann sich die Adresse während einer Sitzung ändern.
- **Sicherheitsbedenken:**
  Bei Internet-basierten Anwendungen müssen Probleme wie Portweiterleitungen und potenziell unerwünschter Zugriff auf Dienste (beispielsweise durch unsichere HTTP-Verbindungen) berücksichtigt werden.
- **Zuverlässigkeit:**
  gRPC über HTTP/2 arbeitet mit TLS und anderen Sicherheitsmaßnahmen, aber die Identifizierung eines Clients nur über seine IP-Adresse kann in komplexen Netzwerken unzuverlässig sein.
