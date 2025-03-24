
Erstellt: 19.03.2025 15:29

Tags:[[Verteilte Systeme]]

# Dokumentation TCP-Server

## main()
- **Argumentprüfung**
  Erwartet zwei Argumente: den Pfad zum Dokumentenstamm (`docroot`) und die Portnummer, auf der der Server lauscht.
- **Socket-Erstellung**
  mit `socket(AF_INET, SOCK_STREAM, 0)` wird ein TCP-Socket erstellt. (Siehe Vorlesung Socket 1)
- **Socketoption setzen**
  Mit `setsockopt()` wird die Option `SO_REUSEADDR` aktiviert, damit der Port nach einem Neustart sofort wiederverwendet wird.
- **Binden und Lauschen**
  Die Methode `bind()` weist dem Socket eine lokale Adresse (alle IPs des Hosts und den angegebenen Port) zu, und `listen()` legt die Länge der Warteschlange fest.
- **Verbindungsannahme und Prozess-Erzeugung**
  In einer Endlosschleife wird mit `accept()` auf neue Verbindungen gewartet. Für jede Verbindung wird ein Kindprozess mit `fork()` erzeugt, der die Anfrage abarbeitet.

```c
int main(int argc, char *argv[]) {
    int socketFileDescriptor, newSocketFileDescriptor;
    socklen_t socketLength;
    struct sockaddr_in serverAddress, clientAddress;
    int reuse = 1;

    if (argc != 3) {
        fprintf(stderr, "Usage: %s <docroot> <port>\n", argv[0]);
        exit(EXIT_FAILURE);
    }
    const char *docroot = argv[1];
    int port = atoi(argv[2]);

    // TCP-Socket erzeugen
    if ((socketFileDescriptor = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
        error("Kann Stream-Socket nicht oeffnen!");
    }

    // Socketoption: sofortige Wiederverwendung der Adresse
    if (setsockopt(socketFileDescriptor, SOL_SOCKET, SO_REUSEADDR, &reuse, sizeof(reuse)) < 0) {
        error("Kann Socketoption nicht setzen!");
    }

    // Lokale Adresse binden
    memset(&serverAddress, 0, sizeof(serverAddress));
    serverAddress.sin_family = AF_INET;
    serverAddress.sin_addr.s_addr = htonl(INADDR_ANY);
    serverAddress.sin_port = htons(port);

    if (bind(socketFileDescriptor, (struct sockaddr *)&serverAddress, sizeof(serverAddress)) < 0) {
        error("Kann lokale Adresse nicht binden!");
    }

    // Warteschlange einrichten
    if (listen(socketFileDescriptor, 10) < 0) {
        error("Fehler bei listen()");
    }

    printf("HTTP Server: läuft auf Port %d mit docroot %s\n", port, docroot);

    // Hauptschleife: Verbindungen annehmen und in Kindprozessen abarbeiten
    for (;;) {
        socketLength = sizeof(clientAddress);
        newSocketFileDescriptor = accept(socketFileDescriptor, (struct sockaddr *)&clientAddress, &socketLength);
        if (newSocketFileDescriptor < 0) {
            perror("Fehler beim accept()");
            continue;
        }

        pid_t pid = fork();
        if (pid < 0) {
            error("Fehler beim Erzeugen eines Kindprozesses!");
        } else if (pid == 0) {  // Kindprozess
            close(socketFileDescriptor);
            handleClient(newSocketFileDescriptor, docroot);
            _exit(0);
        }
        // Elternprozess
        close(newSocketFileDescriptor);
    }
    
    close(socketFileDescriptor);
    return 0;
}
```
## error()
- gibt eine Fehlermeldung mit `perror()` aus und beendet das Programm.
```C
/* err_abort: Ausgabe von Fehlermeldungen und Abbruch */
void error(char *message) {
    perror(message);
    exit(EXIT_FAILURE);
}
```
## getMimeType()
- Bestimmt den MIME-Typ anhand der Dateiendung (.html, .txt, .jpg).
- Verwendet dazu `strrchr()`zum Finden des letzten Punkts in der Zeichenkette und `strcasecmp()`zum fallunabhängigen (ob Groß- oder Kleinbuchstaben) Vergleich.

```C
/* get_mime_type: Bestimmt anhand der Dateiendung den MIME-Typ 
* https://wiki.selfhtml.org/wiki/MIME-Type/%C3%9Cbersicht
* charset=iso-8859-1 (Latin-1) ist für die meisten Textformate geeignet
*/
const char* getMimeType(const char *path) {
    const char *fileExtension = strrchr(path, '.');
    if (!fileExtension) return "application/octet-stream";
    if (strcasecmp(fileExtension, ".html") == 0 || strcasecmp(fileExtension, ".htm") == 0) {
        return "text/html; charset=iso-8859-1";
    } else if (strcasecmp(fileExtension, ".txt") == 0) {
        return "text/plain; charset=iso-8859-1";
    } else if (strcasecmp(fileExtension, ".jpg") == 0 || strcasecmp(fileExtension, ".jpeg") == 0 || strcasecmp(fileExtension, ".jgp") == 0) {
        return "image/jpeg";
    } else if (strcasecmp(fileExtension, ".png") == 0) {
        return "image/png";
    }
    return "application/octet-stream";
}
```
## getFormattedTime()
- Baut einen HTTP-konformen Date-String in GMT-Format mithilfe von `gmtime()` und `strftime()`.

```C
/* get_formatted_time: Erzeugt einen HTTP-konformen Datums-String (GMT) */
void getFormattedTime(char *buffer, size_t size) {
    time_t now = time(NULL);
    struct tm *timeInfo = gmtime(&now);
    strftime(buffer, size, "%a, %d %b %Y %H:%M:%S GMT", timeInfo);
}
```
## send404Page()
- Baut eine einfache HTML-Fehlerseite mit dem Statuscode 404 auf und sendet diese zusammen mit entsprechenden HTTP-Headern an den Client.
- Nutzt `snprintf()`zum Formatieren des Headers und `write()`zum Senden der Daten.

```C
/* send_404: Sendet eine 404-Fehlerseite an den Client */
void send404Page(int clientFileDescriptor) {
    char header[MAXLINE];
    char htmlBody[] = "<html><head><title>404 Not Found</title></head>"
                  "<body><h1>404 Not Found</h1></body></html>";
    char date[128];
    getFormattedTime(date, sizeof(date));
    snprintf(header, sizeof(header),
             "HTTP/1.1 404 Not Found\r\n"
             "Date: %s\r\n"
             "Content-Type: text/html; charset=iso-8859-1\r\n"
             "Content-Length: %lu\r\n"
             "\r\n",
             date, (unsigned long)strlen(htmlBody));
    write(clientFileDescriptor, header, strlen(header));
    write(clientFileDescriptor, htmlBody, strlen(htmlBody));
}
```
## serveFile()
- öffnet die angeforderte Datei im Binärmodus (`"rb"`) mit `fopen()`und überprüft mit `stat()`, ob die Datei existiert und ermittelt deren Größe.
- Baut einen HTTP-Header mit Datum. MIME-Typ und Content-Length und sendet diesen an den Client.
- Liest den Dateiinhalt blockweise mit `fread()`und überträgt ihn mittels `write()`.
- Schließt abschließend die Datei mit `fclose()`.

```C
/* serve_file: Sendet den Inhalt einer Datei (Text oder Binär) an den Client */
void serveFile(int clientFileDescriptor, const char *filepath) {
    FILE *filePointer = fopen(filepath, "rb");  // "rb" steht für read binary
    if (!filePointer) {
        send404Page(clientFileDescriptor);
        return;
    }
    
    struct stat fileStats;
    if (stat(filepath, &fileStats) < 0) {
        fclose(filePointer);
        send404Page(clientFileDescriptor);
        return;
    }
    size_t filesize = fileStats.st_size;
    
    char header[MAXLINE];
    char date[128];
    getFormattedTime(date, sizeof(date));
    const char *mime = getMimeType(filepath);
    
    snprintf(header, sizeof(header),
             "HTTP/1.1 200 OK\r\n"
             "Date: %s\r\n"
             "Content-Type: %s\r\n"
             "Content-Length: %lu\r\n"
             "\r\n",
             date, mime, (unsigned long)filesize);
    write(clientFileDescriptor, header, strlen(header));
    
    char buffer[MAXLINE];
    size_t bytesRead;
    while ((bytesRead = fread(buffer, 1, sizeof(buffer), filePointer)) > 0) {
        write(clientFileDescriptor, buffer, bytesRead);
    }
    fclose(filePointer);
}
```
## serveDirectory()
- Öffnet das Verzeichnis mit `opendir()`und liest dessen Inhalte mit `readdir()`.
- Überspringt die Einträge `"."`und `".."`.
- Baut ein HTML-Dokument, in dem alle gefundenen Dateien und Unterverzeichnisse als Links ausgeführt werden.
- Sendet den generierten HTML-Code zusammen mit passenden Headern an den Client.

```C
/* serve_directory: Erzeugt eine HTML-Seite mit einer Liste aller Einträge im Verzeichnis */
void serveDirectory(int clientFileDescriptor, const char *directoryPath, const char *requestPath) {
    DIR *dir = opendir(directoryPath);
    if (!dir) {
        send404Page(clientFileDescriptor);
        return;
    }
    
    char htmlBody[4096];
    memset(htmlBody, 0, sizeof(htmlBody));
    strcat(htmlBody, "<html><head><title>Directory Listing</title></head><body>");
    strcat(htmlBody, "<h1>Directory Listing</h1><ul>");
    
	/** Auflisten der Verzeichniseinträge
	* - . und .. werden ignoriert
    * - Für jeden Eintrag wird ein Link erstellt
	*   - wenn der aktuelle requestPath ein "/" ist, wird der Link "/eintrag" erstellt
	*   - Andernfalls wird der Link relativ zum aktuellen requestPath erstellt
    */
    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
            continue;
        char link[1024];
        if (strcmp(requestPath, "/") == 0) {
            snprintf(link, sizeof(link), "/%s", entry->d_name);
        } else {
            snprintf(link, sizeof(link), "%s/%s", requestPath, entry->d_name);
        }
        strcat(htmlBody, "<li><a href=\"");
        strcat(htmlBody, link);
        strcat(htmlBody, "\">");
        strcat(htmlBody, entry->d_name);
        strcat(htmlBody, "</a></li>");
    }
    closedir(dir);
    strcat(htmlBody, "</ul></body></html>");
    
    char header[MAXLINE];
    char date[128];
    getFormattedTime(date, sizeof(date));
    snprintf(header, sizeof(header),
             "HTTP/1.1 200 OK\r\n"
             "Date: %s\r\n"
             "Content-Type: text/html; charset=iso-8859-1\r\n"
             "Content-Length: %lu\r\n"
             "\r\n",
             date, (unsigned long)strlen(htmlBody));
    write(clientFileDescriptor, header, strlen(header));
    write(clientFileDescriptor, htmlBody, strlen(htmlBody));
}
```
## handleClient()
- Liest den HTTP-Request vom Client mit `read()`und gibt den Request zur Debug-Zwecken auf der Konsole aus.
- Analysiert die erste Zeile des Requests (Methode, URL, Protokoll) mit `sscanf()`.
- Entfernt etwaige URL-Parameter (alles ab dem Fragezeichen).
- Erzeugt den vollständigen Pfad zur angeforderten Ressource, indem der docroot und die URL kombiniert werden.
- Überprüft mit `stat()`, ob der Pfad zu einer Datei oder einem Verzeichnis führt, und ruft entsprechend `serveFile()`oder `serveDirectory()`auf.
- Bei Fehlern (z.B. Ressource nicht gefunden) wird eine 404-Seite gesendet.

```C
/* handle_client: Liest den HTTP-Request, parst die GET-Anfrage und liefert die gewünschte Ressource */
void handleClient(int clientFileDescriptor, const char *docroot) {
    char buffer[MAXLINE];
    memset(buffer, 0, sizeof(buffer));
    int n = read(clientFileDescriptor, buffer, sizeof(buffer) - 1);
    if (n <= 0) {
        close(clientFileDescriptor);
        return;
    }
    
    // Debug-Ausgabe des Requests
    printf("Received request:\n%s\n", buffer);
    
    char method[16], url[256], protocol[16];
    if (sscanf(buffer, "%15s %255s %15s", method, url, protocol) != 3) {
        close(clientFileDescriptor);
        return;
    }
    
    // Unterstützt werden nur GET-Anfragen
    if (strcmp(method, "GET") != 0) {
        send404Page(clientFileDescriptor);
        close(clientFileDescriptor);
        return;
    }
    
    // Entferne etwaige URL-Parameter (z. B. ?...)
    char *query = strchr(url, '?');
    query ? *query = '\0' : 0;
    
    // Erzeuge den vollständigen Pfad zur angefragten Ressource
    char path[1024];
    
    if (strcmp(url, "/") == 0) {
        snprintf(path, sizeof(path), "%s", docroot);
    } else {
        if (url[0] == '/')
            snprintf(path, sizeof(path), "%s%s", docroot, url);
        else
            snprintf(path, sizeof(path), "%s/%s", docroot, url);
    }
    
    // :)
    //(strcmp(url, "/") == 0) ? snprintf(path, sizeof(path), "%s", docroot) : ((url[0] == '/') ? snprintf(path, sizeof(path), "%s%s", docroot, url) : snprintf(path, sizeof(path), "%s/%s", docroot, url));


    struct stat st;
    
    if (stat(path, &st) < 0) {
        send404Page(clientFileDescriptor);
    } else {
        if (S_ISDIR(st.st_mode)) {
            serveDirectory(clientFileDescriptor, path, url);
        } else if (S_ISREG(st.st_mode)) {
            serveFile(clientFileDescriptor, path);
        } else {
            send404Page(clientFileDescriptor);
        }
    }
    
    // :)
	// (stat(path, &st) < 0) ? send404Page(clientFileDescriptor) : (S_ISDIR(st.st_mode) ? serveDirectory(clientFileDescriptor, path, url) : (S_ISREG(st.st_mode) ? serveFile(clientFileDescriptor, path) : send404Page(clientFileDescriptor)));
    close(clientFileDescriptor);
}
```