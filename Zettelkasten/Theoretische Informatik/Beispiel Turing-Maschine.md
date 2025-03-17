
Erstellt: 17.03.2025 16:58

Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]
# Beispiel Turing-Maschine

+ Hinweis: nicht immer besteht Interesse an der akzeptieren Sprache; es kann auch um erreichende Band-Inhalte gehen

### Wandle alle a in b und alle b in a um:
Wandere kontinuierlich nach links und ändere dabei das gelesene Zeichen um, falls # erreicht wird, ist der Anfang gefunden und die Arbeit beendet.
+ **verkürzend L, R, S für LINKS, RECHTS, STOPP**
+ $TM = (\{Start, \ wandel, \ S\}, \ \{a, \ b, \ \#\}, \ über, \ Start)$ mit
+ über(Start, # ) = (wandle, #, L)
+ über(wandle, a) = (wandle, b, L)   // ändern und weiterlaufen
+ über(wandle, b) = (wandle, a, L)
+ über(wandle, #) = (S, #, S)   // Zustand markiert Ende
+ über(x, y) muss nicht total definiert sein (geht eventuell nicht weiter)

![[Screenshot 2025-03-17 170820.png | Seite 44]] 

### Konstruiere Turing-Maschine TM mit $Lang(TM) = \{a^{n}b^{n + m} \mid n, \;m \ge 0\}$ 
+ Wie immer am Angang: überlege einen Algorithmus
+ neue Zustände, die für bestimmte Situationen stehen
+ neue Zeichen, die sich Informationen auf dem Band merken
+ **nehme b, streiche es, lauf ganz nach links und streiche a, laufe ganz nach rechts streiche b; wenn links am Ende kein a (und Rest nur b), dann gehe in Halte-Zustand**
+ TM = ({Start, entferneB, sucheStelleVorA, entferneA, sucheStelleHinterB, pruefeAufNurB, S}, {a, b, #}, über, Start) mit

+ über(Start, #) = (entferneB, #, L)
+ über(entferneB, #) = (S, #, S)   // gleich viele a und b (auch 0)
+ über(entferneB, b) = (sucheStelleVorA, #, L)
+ über(sucheStelleVorA, x) = (sucheStelleVorA, x, L), $x \in \{a, \ b\}$
+ über(sucheStelleVorA, #) = (entferneA, #, R)
+ über(entferneA, #) = (S, #, S)   // passiert, wenn ein b mehr als a
+ über(entferneA, b) = (pruefeAufNurB, #, R)
+ ==über(entferneA, a) = (sucheStelleHinterB, #, R)==   // highlight im Bild
+ über(sucheStelleHinterB, x) = (sucheStelleHinterB, x, R), $x \in \{a, \ b\}$
+ über(sucheStelleHinterB, #) = (entferneB, #, L)
+ über(pruefeAufNurB, b) = (pruefeAufNurB, #, R)
+ über(pruefeAufNurB, #) = (S, #, S)
  ![[Screenshot 2025-03-17 172542.png | Seite 46]]
### Auf dem Band stehen am Anfang n-Striche (*unäre Darstellung* der Zahl n), am Ende sollen 2n-Striche stehen
Idee: ein neues Trennsymbol (%) setzen, ersten nicht bearbeiteten Strich links suchen, diesen markieren (neues Zeichen ~), nach rechts gehen und zwei Striche anfügen, dann nach Links gehen und ersten nicht bearbeiteten Strich suchen; wenn nicht vorhanden, markierte Striche und Trennsymbol löschen und an das Ende des Ergebnisses laufen.
+ TM = ({Start, sucheStrich, entferneEingabe, maleStriche, zweiterStrich, zumStart, S}, { | , ~, %, #}, über, Start) mit
+ über(Start, #) = (sucheStrich, %, L)   // Trennzeichen setzen
+ über(sucheStrich, #) = (entferneEingabe, #, R)
+ über(sucheStrich, ~) = (sucheStrich, ~, L)
+ über(sucheStrich, | ) = (maleStriche, ~, R)   // Strich verarbeitet
+ über(maleStriche, ~) = (maleStriche, ~, R)
+ über(maleStriche, %) = (maleStriche, %, R)
+ über(maleStriche, | ) = (maleStriche, |, R)
+ über(maleStriche, #) = (zweiterStrich, |, R)
+ über(zumStart, | ) = (zumStart, %, L)
+ über(zumStart, %) = (sucheStrich, %, L)
+ über(entferneEingabe, ~) = (entferneEingabe, #, R)
+ über(entferneEingabe, %) = (entferneEingabe, #, R)
+ über(entferneEingabe, | ) = (entferneEingabe, |, R)
+ über(entferneEingabe, #) = (S, #, S)

![[Screenshot 2025-03-17 175848.png | Seite 51]]

Im folgenden Bild beginnt es oben links und die Abfolge ist von Oben nach Unten und geht dann nach rechts. ->* bedeutet, dass ein paar Schritte übersprungen wurde.  

![[Screenshot 2025-03-17 180420.png | Seite 52]]