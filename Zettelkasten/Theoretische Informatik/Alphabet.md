Tags: [[Theoretische Informatik Entscheidbarkeit]], [[Theoretische Informatik]]

### Definition
Ein *Alphabet* ist eine endliche, nicht-leere Menge. Die Elemente des Alphabets werden [*Zeichen*](remote-data/Zettelkasten/Theoretische%20Informatik/Wort.md) (oder auch *Buchstaben*) genannt. Als Kurzschreibweise wird auch gerne $\sum$ (großes Sigma) verwandt

---
#### Beispiel
- Alphabet1 = {a,b,c,d,e}
- Alphabet2 = {if, while, for}
- Alphabet3 = {$\theta$, $\Leftrightarrow$, $\vee$, $\forall$}

---

### $Alphabet^{*}$ und $Alphabet^{+}$ 
$Alphabet^{*}$ bezeichnet die (unendliche) Menge von [Wörtern](remote-data/Zettelkasten/Theoretische%20Informatik/Wort.md) über dem Alphabet. Mit $Alphabet^{+}$ wird die Menge der Worte über dem Alphabet beschrieben, die mindestens ein Zeichen hat, d.h. $Alphabet^{+} = Alphabet^{*} - \{\epsilon \}$ 

- $Alphabet1^{*} = \{\epsilon , a,b,c,d,e,aa,ab,ac, ...\}$ 
- $Alphabet1^{+} = \{a,b,c,d,e,aa,ab,ac, ... \}$ 