
Erstellt: 17.03.2025 16:37

Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]
## Definition

Die Folge von Konfigurationen K0 -> K1 -> ... -> Kn wird *Rechnung* (bzw. *Berechnung*) einer Turing-Maschine genannt und abkürzend K0 ->* Kn geschrieben, ergänzend gilt K0 ->* K0

## Eigenschaften wenn Maschine anhält

+ Eine Turing-Maschine *hält* in einer Konfiguration, wenn bei dem letzten Ergebnis der genutzten Überführungsfunktion ein STOPP (Stopp-Symbol) steht, die Berechnung heißt dann *terminierende Berechnung;* wird auch als "die Turing-Maschine hält angesetzt auf die Eingabe an" oder "die "Turing-Maschine hält in der Konfiguration" beschrieben.
+ Gibt es eine unendliche Berechnung *divergiert* die Turing-Maschine
+ Eine Turing-Maschine *hängt* in einer Konfiguration (zu, w1 z w2), wenn $w1 = \epsilon$ und über $(zu, \ z) = (. \ , \ . \ , \ LINKS)$ (Versuch über den linken Rand hinaus zu laufen, . für beliebig)
+ Die *akzeptierte Sprache einer Turing-Maschine* TM ist definiert als $Lang(TM) = \{w \in (Alphabet-\{\#\})^{*} \mid \exists K \ (Start, \ \#w\underline\#) ->^{*} \ K \ und \ TM \ hält \ in \ K\}$ 
