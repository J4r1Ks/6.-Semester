Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]

Für ein [[Wort]] lässt sich die i-te Iteration induktiv wie folgt definieren:
- $w^0 = \epsilon$
- $w^{n+1} = ww^n$
- $-\epsilon^0 = \epsilon$     $\epsilon^{42} = \epsilon$     $(ab)^0 = \epsilon$     $(ab)^3 = ababab$ 

Die *Umkehrung* (auch Reverse) eines Wortes lässt sich induktiv über den Aufbau des Wortes definieren:
- ist Wort = $\epsilon$  ist $w^R = \epsilon^R = \epsilon$
- ist Wort = va, und a ist ein Zeichen des [Alphabets](Alphabet), dann ist $(va)^R = a(v^R)$     $-(abba)^R = abba$     $(bba)^R = abb$ 