Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]

## Definition
Eine *Turing-Maschine* ist ein Tupel (Zustände, Alphabet, *Überführungsfunktion*, Start) mit
+ Zustände ist eine endliche nicht leere Menge
+ .# $\in$  Alphabet  (Leerzeichen, blank)
+ Start $\in$ Zustände
+ Überführungsfunktion über: Zustände $\times$ Alphabet -> Zustände $\times$ Alphabet {LINKS, RECHTS, STOPP}

## Konfiguration
Eine Konfiguration einer Turing-Maschine beschreibt vollständig die aktuelle Situation der Maschine, sie ist gegeben durch (z, w1 a w2) mit $z \in Zustände$, $w1 \in Alphabet^{*}$ dem Bandinhalt links des Kopfes, $a \in Alphabet$, dem Zeichen unter dem Lesekopf, $w2 \in Alphabet^{* \ \circ} (Alphabet - \{\#\})  \cup \{\epsilon \}$ dem Bandinhalt rechts des Kopfes (ohne das unendliche leere Band)       