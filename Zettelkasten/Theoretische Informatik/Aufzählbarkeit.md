
Erstellt: 31.03.2025 18:11

Tags:[[Theoretische Informatik]], [[remote-data/Tags/Entscheidbarkeit|Entscheidbarkeit]], [[Turing Maschine]]

# Aufzählbarkeit

## Definition:
Eine $Sprache \subseteq Alphabet*$ heißt *rekursiv aufzählbar*, wenn Sprache = {} oder es eine Turing-Maschine (Zustände, { |, #} $\cup$ Alphabet, Überführungsfunktion, Start) gibt mit $Sprache =  \{w \mid \text{es gibt ein n} \in \mathbb{N}, \text{einen Zustand z und eine terminierende Berechnung mit Start,}\ \#|^{n}\underline\# \rightarrow \text{*} \ z,\ \#w\underline\# \ \}$  
- werden nacheinander $|^n$ Zeichen in die Turing-Maschine eingegeben, wird letztendlich jedes Wort der Sprache als Ergebnis vorkommen (Ergebnisse dürften sogar mehrfach vorkommen)
- "Jedes Wort kommt irgendwann mal vor"
- Worte, die nicht zur Sprache gehören, werden nicht als Ergebnis vorkommen, haben z.B. eine divergierende Berechnung
- Randnotiz: für unendlich viele Wörter werden unterschiedliche Berechnungen benötigt, da Zustandsmenge endlich, gibt es mindestens einen Zustand der sich wiederholt

## alternative Definition:
Sei Sprache $\subseteq$ Alphabet*, eine Turing-Maschine (Zustände, Alphabet', Überführungsfunktion, Start) mit Alphabet $\subseteq$ Alphabet', und Zustand q $\in$ Zustand *zählt die Sprache auf*, wenn $$Sprache = \{w \mid \text{es gibt u} \in \text{Alphabet'* und eine Berechnung mit Start, } \underline\# \rightarrow\text{*} \ \#w\underline\#u\ \}$$Eine Sprache $\subseteq$ Alphabet* heißt *rekursiv aufzählbar,* wenn sie die leere Menge ist oder es eine Turing-Maschine gibt, die sie aufzählt

- Idee ist, dass alle Worte wieder schrittweise aufgezählt werden, dabei wird immer mal wieder der Zustand q besucht (Blink-Zustand), der angibt, dass das nächste Wort gefunden wurde; u ist der Rest der z.B. zum Weiterrechnen benötigt wird
- generell gibt es oft Varianten für Definitionen, betreffen die den gleichen Begriff muss die Äquivalenz der Begriffsdefinitionen gezeigt werden (aus dem einen folgt das andere und andersherum oder durch Äquivalenzumformungen)
References