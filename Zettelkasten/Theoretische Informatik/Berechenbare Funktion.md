
Erstellt: 17.03.2025 18:28

Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]

## Definition

Sei ein Alphabet ohne "#-Zeichen" gegeben. Eine Funktion $$f: (Alphabet^{*})^{m} \rightarrow (Alphabet^{*})^{n}$$ heißt *Turing-Maschinen-berechenbar*, wenn es eine Turing-Maschine $TM = (Zustände, \ Alphabet', \ Überführungsfunktion, \ Start) mit \# \in Alphabet', \ Alphabet \subseteq Alphabet'$ gibt, so dass für alle w1, ..., wm, u1, ..., un $\in$ Alphabet* gilt:
- $f(w1,...,wm) = (u1,...,un)$ genau dann wenn es eine terminierende Berechnung Start, $\#w1\#w2\#...\#wm\underline\# \rightarrow ^{*} z, \ \#u1\#u2\#...\#un\underline\#$ gibt.
- $f(w1,...,wm)$ ist undefiniert genau dann wen TM in $\#w1\#w2\#...\#wm\underline\#$ startet und die Rechnung hängt oder nicht terminiert.
[[Beispiel Turing-Maschine |Beispiel 3]] zeigt, dass $f : Alphabet^{*} \rightarrow Alphabet^{*}$ mit $f(|^{n}) = f(|^{2n})$ und sonst undefiniert, berechenbar ist (genauer Ergebnis noch nach links schieben)

## Wieso mal ohne mal mit $\#$
- In der vorherigen Definition werden Funktionen auf Worten ohne # betrachtet
- Hintergrund ist die Startkonfiguration: $(Start, \ \#w\underline\#)$, das linke Leerzeichen dient zur Erkennung, dass die Maschine vor Eingabe steht; würde w Leerzeichen enthalten, wäre es nicht möglich den Anfang zu erkennen
- Bei $\#w1\#w2\#...\#wm\underline\#$ klar, m-tes Leerzeichen ist beim Durchlauf nach rechts erstes Zeichen vor der Eingabe (mit m Zuständen einfach erreichbar)
- Während der Berechnung können beliebig viele # an beliebigen Stellen stehen
- Sollten in w Leerzeichen stehen sollen, ist die einfach konstruierbar, nutze dazu sonst nicht genutztes Zeichen z und wandele beim ersten Durchlauf von rechts nach links alle z in # um.
