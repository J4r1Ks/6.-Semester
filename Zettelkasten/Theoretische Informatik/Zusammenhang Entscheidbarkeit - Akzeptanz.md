
Erstellt: 31.03.2025 18:04

Tags:[[Theoretische Informatik]], [[remote-data/Tags/Entscheidbarkeit|Entscheidbarkeit]], [[Turing Maschine]]

# Zusammenhang Entscheidbarkeit - Akzeptanz

- Satz: Ist eine Sprache entscheidbar, dann ist sie auch akzeptierbar
- Idee: Nutzung Turing-Maschine für Entscheidbarkeit, wenn die Maschine "N" berechnen will, jage sie in eine Endlosschleife

- Die andere Richtung gilt nicht, da unklar ist, ob die Maschine für Akzeptanz irgendwann anhält

- Satz: Ist eine Sprache und ihr Komplement akzeptierbar, dann ist die Sprache entscheidbar
- Idee: Simuliere an zwei getrennten Stellen auf dem Band die beiden Turing-Maschinen für die Akzeptanz, der Zustandsraum besteht aus dem kartesischen Produkt der beiden Turing-Maschinen; wenn eine anhält lösche alles und gebe Y oder N aus.

References: [[remote-data/Zettelkasten/Theoretische Informatik/Entscheidbarkeit|Entscheidbarkeit]], [[Akzeptanz]]