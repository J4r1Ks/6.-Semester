Tags: [[Theoretische Informatik Grundlagen]], [[Theoretische Informatik]]

==**Definition**==
- eine *Menge* ist eine zusammenfassung von Objekten aus einem vorgegebenen Grundraum, der *Grundmenge*. Ein Objekt kann entweder zur Menge gehören (geschrieben: $Objekt \in Menge$) oder nicht zur Menge gehören (geschrieben $Objekt \not\in Menge$).
- Mengen haben keine Reihenfolge, wird in einer Beschreibung ein Element mehrfach angegeben, ist das zu ignorieren.
$$-\{1,2,3\} = \{3,2,1\} = \{3,3,1,3,2,1\}$$
- kann die Anzahl der Elemente einer Menge angegeben werden, also ist sie einfach hinschreibbar, heißt die Menge *endlich*, sonst *unendlich*.
- die leere Menge, also Menge ohne Elemente wird $\{\}$ oder auch $\varnothing$ geschrieben.

---

- Der *Schnitt* von zwei Mengen enthält alle Elemente, die in beiden Mengen vorkommen, formaler:
$$Menge_{1} \cap Menge_{2} = \{\,e \mid e \in Menge_{1}\ und\ e \in Menge_{2}\}$$
- Die *Vereinigung* von zwei Mengen enthält alle Elemente, die in mindestens einer der beiden Mengen vorkommen, formaler:
$$Menge_{1} \cup Menge_{2} = \{\,e \mid e \in Menge_{1}\ oder\ e \in Menge_{2}\}$$
- Die *Differenz* von zwei Mengen enthält alle Elemente, die in der ersten und nicht in der zweiten Menge vorkommen, formaler:
$$Menge_{1} - Menge_{2} = \{e \mid e \in Menge_{1}\ und e \not\in Menge_{2}\}$$
- Das *Komplement* eine Menge enthält genau die Elemente der Grundmenge, die in der Menge nicht vorkommen, formaler:
$$Komplement(Menge) = \{e \mid e \not\in Menge \ und\ e \in Grunmenge\}$$