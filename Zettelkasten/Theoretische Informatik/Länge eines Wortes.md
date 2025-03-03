Tags: [[Theoretische Informatik Entscheidbarkeit]], [[Theoretische Informatik]]

### Definition
Für ein [[remote-data/Zettelkasten/Theoretische Informatik/Wort]] w bezeichnet $|w|$ die Länge des Wortes, also die Anzahl der Zeichen von w. Für ein Zeichen a aus dem [[Alphabet]] bezeichnet *Anzahl(a, w)* die Anzahl der Vorkommen von a in w.

#### Formal: 
Anzahl: $Alphabet \times Alphabet^{*} \ -> \mathbb{N}$  
- $|\epsilon| = 0$      $|a| = 1$     $|aaa| = 3$      $|whileforwhile| = 3$ 
- Anzahl(a, $\epsilon$) = 0     Anzahl(a, a) = 1     Anzahl(a, abba)  =2