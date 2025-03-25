
Erstellt: 17.03.2025 19:03

Tags:[[Theoretische Informatik]]

## Aufgabe 3 (Ausführung einer Turing-Maschine (Klausurtypisch))


![[Screenshot 2025-03-17 190629.png]]
### a)
Gegeben sei die Turing-Maschine auf der rechten Seite. Geben Sie für die Startworte abc und $\epsilon$ die zugehörigen Berechnungen als Folge von Konfigurationen an, bis keine Folgekonfiguration mehr möglich ist.

![[Theoretische_Turing_Maschine.pdf]]

#### abc)
1. Regel 1
2. Regel 2
3. Regel 7
4. Regel 3
5. Regel 8
6. Regel 5
7. Regel 11
8. Regel 11
9. Regel 12
10. Regel 13

 #### Leeres Wort
1. Regel 1
2. Regel 5
3. Regel 13
### b)
Was vermuten Sie, was die Turing-Maschine generell macht?

Die Turing-Maschine wandelt alle Buchstaben im Alphabet in b um.

## Aufgabe 4 (Erstellung einer Turing-Maschine)
### a)
Zeigen Sie durch die Angabe einer Turing-Maschine, dass die Sprache $\{cca^{n}b^{n} \mid n \ge 0\}$ von einer Turing-Maschine akzeptiert wird. Beschreiben Sie vorher Ihren Ansatz mit mindestens 2 Sätzen.
1. Erst die zwei c: Die Maschine überprüft zuerst, ob am Anfang genau zwei c stehen. Ist dies nicht der Fall, verwirft sie das Wort sofort.
2. Danach $a^{n}b^{n}$ angewendet: Jedes ungelesene a wird markiert und dann das zugehörige (noch unmarkierte) b gesucht und ebenfalls markiert. Sind alle as und bs paarweise markiert, akzeptiert die Maschine, andernfalls verwirft sie.

+ **Zustandsmenge: $$Q = \{q_{start}, q_{c1}, q_{c2}, q_{scanA}, q_{scanB}, q_{back}, q_{accept}, q_{reject}\}$$
+ **Bandalphabet:** $\Sigma_{Band} = \{c,a,b,X,Y,\#\}$ 
	Hier steht X für ein markiertes a und Y für ein markiertes b. # ist ein Leer- bzw. Blank-Symbol
- **Eingabealphabet:** $\Sigma = \{c,a,b\}$
- **Startzustand:** $q_{start}$
- **Akzeptierzustand:** $q_{accept}$
- **Verwerfzustand:** $q_{reject}$ 

### b)
Geben Sie für die Startworte ccab, $\epsilon$, und cb die zugehörigen Berechnungen als Folge von Konfigurationen an, bis keine Folgekonfiguration mehr möglich ist.

#### WORT ccab:
Das Wort beginnt mit genau zwei c, dann folgen a und b. Die Maschine sollte dieses Wort akzeptieren, da es der Form $cc\;a^1b^1$ entspricht.

==**Möglicher Ablauf**==
1. Start: $(q_{start})\; \underline{c}\;c\;a\;b$ 
	- Zustand: $(q_{start})$
	- Gelesenes Symbol: erstes ==c== 
2. Erstes ==c== prüfen
$$\begin{array}
\ (q_{start}, \; c) = (q_{c1}, \;c,\;R) \\[4pt]
(\ q_{c1}\ )\;\;c\;\underline{c}\;a\;b
\end{array}
$$
	Schreibe c zurück, Kopf nach rechts, Zustand $q_{c1}$
3. Zweites ==c== prüfen
$$\begin{array}
\ (q_{c1}, \; c) = (q_{c2}, \;c,\;R) \\[4pt]
(\ q_{c2}\ )\;\;c\;c\;\underline{a}\;b
\end{array}
$$
	Schreibe c zurück, Kopf nach rechts, Zustand $q_{c2}$
4. Erstes umarkiertes ==a== finden
$$\begin{array}
\ (q_{c2}, \; a) = (markiere\; a \rightarrow X, \;R,\;q_{scanB}) \\[4pt]
(\ q_{scanB}\ )\;\;c\;c\;X\;\underline{b}
\end{array}
$$
	- Markiere das a zu X, bewege Kopf nach rechts, Zustand $q_{scanB}$
5. Passendes ==b== finden und markieren
$$\begin{array}
\ (q_{c2}, \; a) = (markiere\; a \rightarrow X, \;R,\;q_{scanB}) \\[4pt]
(\ q_{back}\ )\;\;c\;c\;\underline{X}\;Y
\end{array}
$$
	- Markiere b zu Y, Kopf nach links, Zustand $q_{back}$ 
6. Zurücklaufen an den Anfang von $a^nb^n$
	Die Maschine läuft nun im Zustand $q_{back}$ nach links, bis sie wieder links vom ersten markierten Symbol oder bei den beiden c ist. Schließlich kehrt sie in den Zustand $q_{c2}$ zurück, um zu prüfen, ob es noch ein unmarkiertes a gibt.
	Angenommen, sie sieht:
	- X (markiertes a): einfach weiter links,
	- c: weiter links,
	- ==#== (oder Bandanfang) $\rightarrow$ wechsle nach rechts + Zustand $q_{c2}$
	Am Ende steht sie wieder rechts von den beiden c:
	$$\ (\ q_{c2}\ ) \;\;\;c\;c\;X\;Y$$
	(Kopf irgendwo bei X oder direkt dahinter, je nach exakter Realisierung.)
7. Check: keine unmarkierten a mehr?
	- Im Zustand $q_{c2}$ :
		- Liest sie X oder Y, bedeutet das: schon markiert, weiter rechts ...
		- Irgendwann trifft sie auf Blank oder Bandende $\rightarrow$ keine unmarkierten a mehr gefunden
		- Nun prüft sie, ob es noch unmarkierte b gibt. Falls nein $\rightarrow$ akzeptiere.
	Tatsächlich findet sie keine weiteren a. Sie auch findet kein unmarkiertes b, weil wir das einzige b bereits zu Y gemacht haben.

==**Ergebnis: ccab wird akzeptiert**==

#### Wort $\epsilon$ (leeres Wort):
Das leere Wort enthält keine Symbole. Die Sprache erfordert jedoch zwei c am Anfang. Da $\epsilon$ keine c enthält, sollte die Maschine verwerfen.

==**Ergebnis: $\epsilon$ wird verworfen**==

#### Wort cb:
==**Ergebnis: cb wird verorfen**==

## Aufgabe 5 (Ausführung einer Turing-Maschine)
### a)
