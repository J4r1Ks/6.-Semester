Tags: [[Theoretische Informatik Grundlagen]], [[Theoretische Informatik]]

### Definition
Die *Potenzmenge* einer Menge ${M}$ ist definiert als die Menge aller Teilmengen von ${M}$. Man bezeichnet sie oft mit $\mathcal{P}(M)$ oder $2^M$, formaler: $$\mathcal{P}(M)=\{e \mid e \subseteq M\}$$ Das bedeutet, jedes Element der Potenzmenge ist selbst eine Menge, die ein Teil von ${M}$ ist.

Ist ${M}$ endlich mit ${n}$ Elementen, dann hat die Potenzmenge $\mathcal{P}(M)$ genau $2^n$ Elemente. Dies folgt aus der Tatsache, dass jedes Element entweder in einer Teilmenge enthalten sein kann oder nicht.

---
#### Beispiel für eine endliche Menge:
Sei ${M} = \{a,b\}$. Dann sind alle möglichen Teilmengen:
- Die leere Menge: $\emptyset$ 
- Ein-Tupel: $\{a\}$ und $\{b\}$ 
- Die ganze Menge: $\{a,b\}$
	somit ist:
	$$\mathcal{P}(M) = \{\emptyset, \{a\}, \{b\}, \{a,b\}$$