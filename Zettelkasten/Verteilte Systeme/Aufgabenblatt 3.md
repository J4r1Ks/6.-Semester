
Erstellt: 01.04.2025 16:34

Tags:[[Verteilte Systeme]]

# Aufgabenblatt 3

![[VS_SoSe25_Blatt_03_Aufgabe.pdf]]

## Beantworten sie folgende folgende Fragen zu ihrer Lösung

### Was ist speziell bei Verwendung von UDP als Transportprotokoll zu berücksichtigen?
- UDP ist verbindungslos. Es wird kein Verbindungsaufbau wie bei TCP durchgeführt.
- Es gibt keine Garantie, dass die Pakete ankommen oder in der richtigen Reihenfolge eintreffen.

### Warum sollte für die Umsetzung des Protokolls UDP und nicht TCP verwendet werden?
- UDP sorgt für einen einfachen und schnellen Nachrichtenaustausch.
- Durch die angeforderten Chunks kann man gut nachvollziehen, was eventuell bei der Übertragung verloren ging.

### Wie kann man das Ende einer Datentransfer-Sitzung erkennen? Beschreiben Sie die bei Ihrer Lösung ausgetauschten Nachrichten durch ein Sequenzdiagramm.
#### **Erkennung des Endes:**
  Das Ende wird erkannt, wenn der vom Server gelesene Chunk weniger Bytes enthält als die vereinbarte Chunk-Größe. Das signalisiert, dass das Dateiende erreicht wurde.
```c
void processGet(int socketFileDescriptor, char *message, struct sockaddr_in *clientAddress, socklen_t addressLength) {

...

// Wenn weniger als chunkSize Bytes gelesen wurden, ist das Dateiende erreicht.
    if (bytesRead < (size_t)session->chunkSize) {
        removeSession(session);
    }
    ...
}
```

#### **Sequenzdiagramm**

1. INIT:
	- Client $\rightarrow$ Server: `HSOSSTP_INITX;<chunk size>;<filename>`
	- Server $\rightarrow$ Client: `HSOSSTP_SIDXX;<session key>`

2. Datenübertragung:
	- Client $\rightarrow$ Server: `HSOSSTP_GETXX;<session key>;0` (0 weil erster Chunk angefordert wird)
	- Server $\rightarrow$ Client: `HSOSSTP_DATAX;0;<actual chunk size>;<data>`
	- (Wiederholung mit fortlaufender Chunk Number)
3. Sitzungsende:
	- Wenn ein Chunk gesendet wird, bei dem `<actual chunk size> < <chunk size>` gilt, schließt der Server die Session.
Dadurch weiß der Client, dass der letzte Chunk empfangen wurde und die Übertragung abgeschlossen ist.

### Wie werden Datentransfer-Sitzungen im Server terminiert?

- **Sitzung beenden bei Dateiende:**
  Wenn beim Lesen eines Chunks weniger Bytes als die vereinbarte Chunk-Größe geliefert werden, wird die entsprechende Session mittels der Methode `removeSession(session)`beendet.
- **Fehlerfälle**
  Bei Fehlern (z.B. ungültiger Offset oder Lesefehlern) wird ebenfalls eine Fehlermeldung an den Client gesendet und die Session beendet.

### Was passiert bei Wiederaufnahme einer Sitzung?

- **Wiederaufnahme nicht vorgesehen:**
  In der Implementierung wird eine Sitzung beim Dateiende oder bei Fehlern beendet. Es gibt keinen Mechanismus, um eine bereits abgeschlossene oder abgebrochene Sitzung wieder aufzunehmen.
- **Falls der Client erneut dieselbe Session-Key nutzt:**
  Wird beim erneuten GET der Session-Key nicht gefunden, reagiert der Server mit einer Fehlermeldung (z.B. `HSOSSTP_ERROR;NOS: keine Session vorhanden`).
  
### Was passiert beim Zugriff auf eine nichtexistierende Sitzung?
- Fehlermeldung:
  Wenn der Client einen GET-Befehl mit einem ungültigen oder abgelaufenen Sitzungsschlüssel sendet, durchsucht der Server eine Session-Tabelle und findet keine passende Sitzung.
  - In diesem Fall wird der Server die Nachricht `HSOSSTP_ERROR;NOS: keine Session vorhanden` zurücksenden.
```c
sessionStruct *findSession(int sessionKey) {
    for (int i = 0; i < MAX_SESSIONS; i++) {
        if (sessions[i].active && sessions[i].sessionKey == sessionKey) {
            return &sessions[i];
        }
    }
    return NULL;
}
```

### Wie groß kann die Chunk-Size maximal eingestellt werden?
- **Implementierungsabhängigkeit:**
  Das Maximum wird durch die Größe des Buffers begrenzt, die im Code verwendet werden.
	- Im Code für den Server wird ein Buffer (`databuffer`) mit einer festen Größe von 8192 Bytes verwendet.
- **Praktische Grenze:**
  Das bedeutet, dass die Chunk-Size in dieser Implementierung maximal 8192 Bytes betragen sollte.
	- Theoretisch könnte man den Code so erweitern, dass größere Chunks verarbeitet werden, indem man die Daten in mehreren Paketen sendet, aber in dieser Implementierung ist 8192 Bytes der Höchstwert

## UDPserver.c

### Struktur und Initialisierung
- **PORT** ist durch die Aufgabe vorgegeben und ist auf 8999 definiert
- **MAX_BUFFER** Größe des Buffers für eingehende/senden Daten 
- MAX_SESSION maximale Anzahl der Sessions
- Initialisiere alle Sessions mit `initSessions()`
#### Struktur: sessionStruct
Diese Struktur speichert Informationen zu einer aktiven Transfersitzung:
- **sessionKey**: Ein eindeutiger Schlüssel, der der Sitzung zugewiesen wird.
- **filePointer**: ein FILE* -Pointer, der auf die geöffnete Datei zeigt.
- **chunkSize**: Die Größe der Datenblöcke (chunks), die gesendet werden.
- **filename**: Der Name der Datei (maximal 256 Zeichen).
- **active**: Ein Flag (0 == false, alles andere true), das anzeigt, ob die Session aktiv ist.
Global gibt es ein Array `sessions[MAX_SESSIONS]`und einen globalen Zähler `nextSessionKey`, der fortlaufend Sitzungsschlüssel vergibt.
```c
#define PORT 8999
#define MAX_BUFFER 4096
#define MAX_SESSIONS 100

typedef struct {
    int sessionKey;
    FILE *filePointer;
    int chunkSize;
    char filename[256];
    int active;
} sessionStruct;

sessionStruct sessions[MAX_SESSIONS];
int nextSessionKey = 1;

// 0 == false, alles andere ist true
void initSessions() {
    for (int i = 0; i < MAX_SESSIONS; i++) {
        sessions[i].active = 0;
    }
}
```

### createSession()
- Öffne File
- Suche freien Platz im Array
- aktiviere freien Platz
- kopiere den filename in der session
```c
sessionStruct *createSession(int chunkSize, const char *filename) {
    FILE *filePointer = fopen(filename, "rb");
    if(!filePointer) {return NULL;}

    // Suche freien Platz im Session-Array
    for (int i = 0; i < MAX_SESSIONS; i++) {
        if (!sessions[i].active) {
            sessions[i].active = 1;
            sessions[i].sessionKey = nextSessionKey++;
            strncpy(sessions[i].filename, filename, sizeof(sessions[i].filename) - 1);
            sessions[i].filePointer = filePointer;
            return &sessions[i];
        }
    }
    fclose(filePointer);
    return NULL;
}
```

### findSession()
- sucht die spezifische Session anhand der Session Key
- Falls keine Session mit dem Schlüssel gefunden wird, wird NULL zurückgegeben.
```C
sessionStruct *findSession(int sessionKey) {
    for (int i = 0; i < MAX_SESSIONS; i++) {
        if (sessions[i].active && sessions[i].sessionKey == sessionKey) {
            return &sessions[i];
        }
    }
    return NULL;
}
```

### removeSesion()
- Wenn die Session nicht NULL ist, wird die FILE geschlossen (Falls vorhanden). Anschließend wird die Session mit der Flagge `session->activate` deaktiviert. 
```c
void removeSession(sessionStruct *session) {
    if (session) {
        if (session->filePointer) {
            fclose(session->filePointer);
        }
        session->active = 0;
    }
}
```

### sendMessage()
- In dieser Methode werden alle Nachrichten, die keine Datenpakete sind verarbeitet.
- Wichtig ist, dass an diesen Nachrichten am Ende der ein `\n` angehangen wird.
```c
// Sendet eine Protokollnachricht und hängt automatisch ein '\n' an.
void sendMessage(int socketFileDescriptor, const char* message, int messageLength, struct sockaddr_in *clientAddress, socklen_t addressLength) {
    char sendBuf[MAX_BUFFER];
    int len = snprintf(sendBuf, sizeof(sendBuf), "%.*s\n", messageLength, message);
    sendto(socketFileDescriptor, sendBuf, len, 0, (struct sockaddr *)clientAddress, addressLength);
}
```

### processInit()
- Wenn in der eingehenden Message nicht ausreichend viele Argumente geliefert werden, wird eine Error-Message gesendet.
- Falls die angeforderte Datei nicht existiert, wird eine Error-Message gesendet.
- Falls alles okay ist, wird `HSOSSTP_SIDXX;<session key>` versendet

```c
void processInit(int socketFileDescriptor, char* message, struct sockaddr_in *clientAddress, socklen_t addressLength) {
    // Format: HSOSSTP_INITX;<chunk size>;<filename>
    int chunkSize;
    char filename[256];
    char reply[512];
    
    if (sscanf(message, "HSOSSTP_INITX;%d;%255s", &chunkSize, filename) != 2) {
        int length = snprintf(reply, sizeof(reply), "HSOSSTP_ERROR;CNF: invalid init message");
        sendMessage(socketFileDescriptor, reply, length, clientAddress, addressLength);
        return;
    }
    
    sessionStruct *session = createSession(chunkSize, filename);
    if (!session) {
        int length = snprintf(reply, sizeof(reply), "HSOSSTP;FNF: angeforderte Datei kann nicht gefunden werden");
        sendMessage(socketFileDescriptor, reply, length, clientAddress, addressLength);
        return;
    }
    
    int replyLength = snprintf(reply, sizeof(reply), "HSOSSTP_SIDXX;%d", session->sessionKey);
    sendMessage(socketFileDescriptor, reply, replyLength, clientAddress, addressLength);
}
```

### processGet()
- Wenn nicht ausreichend viele Argumente existieren, dass wird eine Error-Message gesendet.
- Wenn keine gültige Session gefunden wird, wird eine Error-Message gesendet.
- Falls der ReadPointer nicht richtig gesetzt werden kann, wird eine Error-Message gesendet.
- Es wird versucht, `chunkSize`Bytes aus der Datei in den Buffer `dataBuffer`zu lesen. Wenn es nicht funktioniert, wird eine Error-Message gesendet.
- Ein Header-String wird aufgebaut und in `sendBuffer` kopiert. 

```c
void processGet(int socketFileDescriptor, char *message, struct sockaddr_in *clientAddress, socklen_t addressLength) {
    char sendBuffer[16384];

    // Format: HSOSSTP_GETXX;<Session key>;<chunk no>
    int sessionKey, chunkNo;
    if (sscanf(message, "HSOSSTP_GETXX;%d;%d", &sessionKey, &chunkNo) != 2) {
        int length = snprintf(sendBuffer, sizeof(sendBuffer), "HSOSSTP_ERROR;NOS: keine Session vorhanden");
        sendMessage(socketFileDescriptor, sendBuffer, length, clientAddress, addressLength);
        return;
    }

    sessionStruct *session = findSession(sessionKey);
    if (!session) {
        int length = snprintf(sendBuffer, sizeof(sendBuffer), "HSOSSTP_ERROR;NOS: keine Session vorhanden");
        sendMessage(socketFileDescriptor, sendBuffer, length, clientAddress, addressLength);
        return;
    }
  

    // Position im File: chunkNo * chunkSize
    long offset = (long)chunkNo * session->chunkSize;
    if (fseek(session->filePointer, offset, SEEK_SET)) {
        int length = snprintf(sendBuffer, sizeof(sendBuffer), "HSOSSTP_ERROR;CNF: angeforderter Chunk kann nicht transferiert werden");
        sendMessage(socketFileDescriptor, sendBuffer, length, clientAddress, addressLength);
        return;
    }
    
    char dataBuffer[8192];
    int bytesToRead = session->chunkSize;
    size_t bytesRead = fread(dataBuffer, 1, bytesToRead, session->filePointer);
    if (bytesRead == 0 && ferror(session->filePointer)) {
        int length = snprintf(sendBuffer, sizeof(sendBuffer), "HSOSSTP_ERROR;CNF: angeforderter Chunk kann nicht transferiert werden");
        sendMessage(socketFileDescriptor, sendBuffer, length, clientAddress, addressLength);
        return;
    }
  

    // Aufbau der Antwort: HSOSSTP_DATAX;<chunk no>;<bytesRead>;<data>
    char header[128];
    int headerLength = snprintf(header, sizeof(header), "HSOSSTP_DATAX;%d;%zu;", chunkNo, bytesRead);
    // Kopiere Header in den Sende-Puffer
    memcpy(sendBuffer, header, headerLength);
    // Kopiere die Datei-Daten direkt hinter den Header
    memcpy(sendBuffer + headerLength, dataBuffer, bytesRead);
    int totalLength = headerLength + bytesRead;
    // Hier wird KEIN '\n' angehängt, damit die Datei-Daten unverändert bleiben.
    sendto(socketFileDescriptor, sendBuffer, totalLength, 0, (struct sockaddr *)clientAddress, addressLength);
  
    // Wenn weniger als chunkSize Bytes gelesen wurden, ist das Dateiende erreicht.
    if (bytesRead < (size_t)session->chunkSize) {
        removeSession(session);
    }
    // Beispielhafte Ausgabe (falls nötig):
    printf("SessionKey: %d, ChunkNo: %d\n", sessionKey, chunkNo);
}
```