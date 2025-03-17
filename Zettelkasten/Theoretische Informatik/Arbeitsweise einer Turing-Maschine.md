
Erstellt: 17.03.2025 16:18

Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]
# Arbeitsweise einer Turing-Maschine

Gegeben sei eine Turing-Maschine (Zustände, Alphabet, *Überführungsfunktion*, Start) zu $\in$
Zustände $w1, w2 \in Alphabet^{*}$, $z1 \in Alphabet$, $z2 \in Alphabet \cup \{\epsilon\}$ in einer Konfiguration $K1 = (zu, \ w1z1 \ a \ z2w2)$ und sei über $(zu, \ a) = (zu2, \ b, \ X)$, dann berechnet sich die Nachfolgekonfiguration K2, geschrieben K1 -> K2 wie folgt:

+ Sei $X = STOPP \ : \ K2 = (zu2,\ w1z1 \ z2w2)$
+ Sei $X = LINKS \ : \ K2 = (zu2,\ w1z1 \ z2w2)$
+ Sei $X = RECHTS \ : \ K2 = (zu2,\ w1z1b \ z2 \ w2)$, wenn $z2 \not= \epsilon$
	+ $K2 = (zu2, \ w1z1b \ \# \ \epsilon), \ z2 = \epsilon$ 

![[Screenshot 2025-03-17 162734.png]]

Gegeben sei ein Wort w ohne Leerzeichen, $w \in (Alphabet - \{\#\})^{*}$, dann heißt $K0 = (Start, \#w \ \# \ \epsilon) \ (kurz: (Start, \#w \underline\#)$ *Startkonfiguration*; das Leerzeichen # am Anfang markiert den Wortanfang. 
