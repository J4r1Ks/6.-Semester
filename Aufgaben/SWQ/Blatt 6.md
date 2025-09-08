
Erstellt: 23.04.2025 14:51

Tags:[[Software Qualität]]

# Aufgabe 12

## Mit dem vorgestellten Datenfluss-Ansatz kann geprüft werden, ob die Deklaration einer Variablen überflüssig ist, wie?

Für jede Variable wird geprüft:
- Wo wird sie definiert? ( def )
- Wo wird sie danach verwendet, ohne dass sie zwischenzeitlich neu definiert wurde? ( use )
==Wenn für eine bestimmte Definition keine Verwendung entlang eines Pfades gefunden wird, ist sie überflüssig!==

### Vorgehen zur Erkennung überflüssiger Deklarationen

1. Alle Definitionen einer Variable markieren.
2. Alle möglichen Kontrollflusspfade bestimmen
3. Für jede Definition prüfen:
   - Gibt es mindestens eine Verwendung entlang eines Pfades, ohne dass die Variable dazwischen neu gesetzt wurde?
1. Wenn nicht, ist die Definition überflüssig. 

# Aufgabe 13

Markieren Sie den Graphen auf der linken Seite mit def(.), c-use(.), p-use(.).
Ergänzen Sie die Markierungen für dcu(.,.) und dpu(.,.). Geben Sie dann jeweils eine möglichst kleine Menge von Testfällen an, die zusammen das
a) all defs-Kriterium
b) all-p-uses-Kriterium
c) all c-uses-Kriterium
d) all uses-Kriterium
erfüllen. Es reicht die Angabe von Knotenfolgen, z.B. (in, 1, 2, 3, 4, 5, 6). 

- dcu(def, use) $\rightarrow$ Datenfluss von einer Definition zur c-use (z.B. in `if`, `while`)
- dpu(def, use) $\rightarrow$ Datenfluss von einer Definition zur p-use (z.B. in `return`, Zuweisungen)

[[Aufgabe 13.canvas|Zur Lösung]]

## a) all-defs-Kriterium
> Für jede Definition einer Variablen muss es **mindestens einen Pfad** zu einer Verwendung geben.


## b) all-p-uses-Kriterium
>Für jede Definition muss es einen Pfad zur **p-use** geben (z.B. Rückgabe oder Berechnung, keine Bedingungen!).

**p-use für `erg`:** 
- `return erg + 1` in Knoten 6

**Testfälle (Pfade)**
- (1,2,4,6)
- (1,2,3,4,6)
- (1,2,4,5,6)
**Alle Definitionen erreichen p-use in 6**

## c) all-c-uses-Kriterium
>Für jede Definition muss es **mindestens eine Verwendung in einer Bedingung (c-use)** geben.

**Definitionen:**
- `erg` in 1 $\rightarrow$ verwendet in 6
- `erg` in 3 $\rightarrow$ verwendet in 6
- `erg` in 5 $\rightarrow$ verwendet in 6
  
**Testfälle (Knotenpfade):**
- (1,2,4,6) $\rightarrow$ nutzt `erg` aus 1
- (1,2,3,4,6) $\rightarrow$ nutzt `erg` aus 3
- (1,2,4,5,6) $\rightarrow$ nutzt `erg` aus 5


## d) all-uses-Kriterium
>Kombination aus **all c-uses** und **all p-uses**

Da `erg`keine c-use hat, ist das All-Uses-Kriterium hier äquivalent zu **All-P-Uses**
$\rightarrow$ Erfüllt durch gleiche Pfade