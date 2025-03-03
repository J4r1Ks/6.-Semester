Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]

### Definition
Eine *Sprache* über ein [[Alphabet]] ist eine Teilmenge von $Alphabet^{*}$
$$-\{\epsilon\},\ \{\epsilon,a,aa\}, \ \{a^n:n\in \mathbb{N}\}, \ Alphatbet1^{*}$$
sind Beispielsprachen über Alphabet1 (Alphabet1 = {a,b,c,d,e})

#### Operationen
Die [Operationen](Konkatenation) auf Wörtern werden wie folgt auf Sprachen erweitert:
- $Sprace1 ° Sprache2 = \{uv \mid u \in Sprache1,\ v \in Sprace2\}$ , kürzer geschrieben als Sprache1Sprache2
- $\{\epsilon\} ° \{\epsilon,a,aa\} = \{\epsilon,a,aa\}$          $\{\} ° \{\epsilon,a,aa\} = \{\}$ 
- $\{a^n : n \in \mathbb{N}\} ° \{a\} = \{a^n : n \in \mathbb{N}, \ n > 0\}$ 
- $Sprache^0 = \{\epsilon\}$     $Sprache^{n+1} = SpracheSprache^n$   ,    d.h. auch $\{\}^0 = \{\epsilon \}$ 

Der *Kleene-Stern-Abschluss* eine Sprache ist definiert als Vereinigung aller Iterationen der Sprache
- $Sprache^{*} = Sprache^0\ \cup ... \cup\ Sprache^n, n \in \mathbb{N}$  (unendliche Vereiningung)