
Erstellt: 31.03.2025 17:36

Tags:[[Theoretische Informatik]], [[remote-data/Tags/Entscheidbarkeit|Entscheidbarkeit]], [[Turing Maschine]]

# Entscheidbarkeit

## Definition:
Gegeben sei eine Sprache Alphabet*, aus formalen Gründen mit $\{\#, N, Y\} \subseteq \text{Alphabet}$. Gegeben sei eine Turing-Maschine (Zustände, Alphabet', Überführungsfunktion, Start) mit $\text{Alphabet} \subseteq \text{Alphabet'}$.
- Die Turing-Maschine entscheidet die Sprache, falls für alle Wörter $\text{w} \in \text{Alphabet*}$ eine terminierende Berechnung existiert mit:
  $$
\text{Start, \#w\#} \rightarrow \text{*} \{\text{z, \ \# Y}\underline{\#} \ \ \ \ \text{falls} \ w \in Sprache\} \newline
$$
$$\text{Start, \#w\#} \rightarrow \text{*} \{\text{z, \ \# N}\underline{\#} \ \ \ \ \text{sonst}\}$$
- anschaublich, die Turing-Maschine hält garantiert und berechnet ob das eingegebene Wort zur Sprache gehört oder nicht.
- Eine Sprache heißt *entscheidbar* (oder rekursiv), falls eine Turing-Maschine existiert, die die Sprache entscheidet. 