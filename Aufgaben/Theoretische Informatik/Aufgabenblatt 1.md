Tags: [[Theoretische Informatik]]

## Aufgabe 1 (Grundbegriffe, Abzählbarkeit Sprachen, Mathematik versus Informatik)

#### a) 
Gegeben seien folgende Mengen M1= {}, M2={a,b,c}, M3={5,10}. Berechnen Sie
	i) M2 $\cup$ M1 = {a, b, c}
	ii) M3 $\cap$ M3 = {5,10}
	iii) M3 – M2 = {5,10}
	iv) M1 $\times$ M2 = {a, b, c}
	v) M2 $\times$ M3 = {a5, a10, b5, b10, c5, c10}
	vi) (M2 $\times$ M3) $\cap$ (M3 $\times$ M2) = {a5 ,a10, b5, b10, c5, c10}
	vii) Pot(M2) = {$\emptyset$, {a}, {b}, {c}, {ab}, {ac}, {bc}, {abc}}

#### b) 
Begründen Sie formal, warum die Menge {1, 2, 3, 4} abzählbar ist.
	Eine Menge heißt abzählbar, wenn sie entweder endlich ist oder es eine surjektive Funktion der natürlichen Zahlen ist. Diese Menge ist endlich also auch abzählbar.

#### c) 
Begründen Sie formal, warum die Menge $\mathbb{N}\times\mathbb{N}\times\mathbb{N}$ abzählbar ist.
	Die Menge $\mathbb{N}$ ist per Definition abzählbar. Es gilt allgemein, dass das kartesische Produkt zweier Mengen wieder abzählbar ist. Dies kann man mithilfe eines Diagonalarguments oder durch Konstruktion einer bijektiven Abbildung (wie der Cantor-Paarungsfunktion) zeigen.

#### d) 
Sind folgende Mengen Alphabete?

|                                                   | ist Alphabet | ist kein Alphabet |
| ------------------------------------------------- | ------------ | ----------------- |
| {a, b, c}                                         | X            |                   |
| {a, a, b, c, c}                                   | X            |                   |
| {}                                                |              | X                 |
| {$\epsilon$}                                      |              | X                 |
| alle ganzen Zahlen des Intervalls $[-42, 42]$     | X            |                   |
| alle rationalen Zahlen des Intervalls $[-42, 42]$ |              | X                 |
| alle reellen Zahlen des Intervalls $[0,1]$        |              | X                 |
| $\{x \mid x \in \mathbb{N}\ mit \ x+2 < 2 \}$     | X            |                   |
#### e) 
Gegeben seien die folgenden 4 Worte über dem Alphabet {a,b,c}, geben Sie jeweils die
Konkatenation der folgenden Worte untereinander an.

| °          | $\epsilon$         | a           | aa           | bab           |
| ---------- | ------------------ | ----------- | ------------ | ------------- |
| $\epsilon$ | $\epsilon\epsilon$ | $\epsilon$a | $\epsilon$aa | $\epsilon$bab |
| a          | a$\epsilon$        | aa          | aaa          | abab          |
| aa         | aa$\epsilon$       | aaa         | aaaa         | aabab         |
| bab        | bab$\epsilon$      | baba        | babaa        | babbab        |
#### f) 
Gegeben seien die folgenden Sprachen über dem Alphabet {a,b}, welche der Sprachen
enthält das Wort a42?
	i) Sprache1={aaaa}
	ii) Sprache2 = {aaa}
	iii) Sprache1*
	iv) Sprache2*
	v) (Sprache1Sprache2)*
	vi) (Sprache1Sprache1)*
	
##### Antwort: 
- iv) Alle Wörter der Form $(a^3)^k$ für k $\ge$ 0, also $a^{3k}$. Da 42/3 = 14 ist, gilt also $a^{42} = (a^3)^{14}$. 
- v) $a^4+a^3=a^7$ -> $(a^7)^k$ für k $\ge$ 0, also $a^{7k}$. Da 42/7 = 6 ist, gilt also $a^{42} = (a^7)^6$. 

#### g) 
Gegeben sei folgende Java-Klasse. Begründen Sie warum next() in Java aus Sicht der
Mathematik nicht Funktion genannt wird. Überlegen Sie, wie trotzdem der Begriff
Funktion für next passend sein könnte. Überlegen Sie jeweils welche Definitionsbereiche
und Werte- (oder Ergebnis-)bereiche eine Rolle spielen könnten.
```Java
public class Delta {
	private int delta;
	public int next(int wert) {
		return wert + (delta++);
	}
}
```
##### Antwort:
next ist nicht nur von "wert", sondern auch von "delta" abhängig. Das bedeutet, dass derselben Wert von "wert" unterschiedliche Ergebnisse liefert. Wenn delta = 0 und wert = 5 ist, dann ist beim ersten Aufruf von next() wert = 5 und beim zweiten Aufruf wert = 6. Dies widerspricht der mathematischen Anforderungen an eine Funktion, für ein gegebenes Argument stets dasselbe Ergebnis zu liefern.  

Man kann die Methode next als mathematische Funktion betrachten, wenn man den internen Zustand als zusätzlichen Parameter in die Abbildung einbezieht. Man definiert also eine Funktion $$f : \mathbb{Z} \times \mathbb{Z} \rightarrow \mathbb{Z} \times \mathbb{Z}$$
mit $$f(\delta, wert) = (\delta + 1,\ wert + \delta)$$
Hierbei gilt:
**Definitionsbereich:** $\mathbb{Z} \times \mathbb{Z}$ 
- Der erste Teil der Eingabe entspricht dem aktuellen Zustand von delta (angenommen, $\delta \in \mathbb{Z}$)
- Der zweite Teil entspricht dem Parameter wert.
**Ergebnisbereich (Wertebereich):** $\mathbb{Z} \times \mathbb{Z}$ 
- Das erste Element des Ergebnisses ist der neue Zustand von $\delta + 1$ 
- Das zweite Element ist der Rückgabewert $wert + \delta$ 

Auf diese Weise wird die Methode next zu einer rein mathematischen Abbildung, die jedem Eingabepaar (aktueller Zustand, Eingabewert) genau ein Ausgabepaar (neuer Zustand, Ergebnis) zuordnet. Somit erfüllt sie die mathematische Definition einer Funktion, sofern der Zustand explizit als Eingabeparameter betrachtet wird.