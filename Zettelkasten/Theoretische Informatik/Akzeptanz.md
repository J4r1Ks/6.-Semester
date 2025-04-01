
Erstellt: 31.03.2025 17:58

Tags:[[Theoretische Informatik]], [[remote-data/Tags/Entscheidbarkeit|Entscheidbarkeit]], [[Turing Maschine]]

# Akzeptanz

## Definition:
Gegeben sei eine Turing-Maschine (Zustände, Alphabet, Überführungsfunktion, Start)
- Die Turing-Maschine akzeptiert ein Wort $\in$ Alphabet*, falls die Turing-Maschine bei Eingabe des Wortes eine terminierende Berechnung hat (also anhält)
- Die Turing-Maschine akzeptiert eine Sprache, falls für alle Wörter $w \in Alphabet*$ gilt:
	  die Turing-Maschine akzeptiert w gdw. $w \in Sprache$
- Eine Sprache heißt akzeptierbar, wenn es eine Turing-Maschine gibt, die die Sprache akzeptiert

- für Akzeptanz reicht es aus, dass die Turing-Maschine bei zur Sprache gehörenden Worten irgendwann anhält; es ist aber unklar, ob sie dies wirklich macht (wäre zu zeigen) 
