
Erstellt: 26.03.2025 22:15

Tags:[[Software Qualität]]

# Aufgabe 6

## a)
- Jedes Element muss größer oder gleich dem nachfolgenden Element sein, wenn in absteigender Reihenfolge sortiert wird.
- Die zurückgegebene Liste enthält exakt dieselben Elemente mit gleicher Anzahl wie die Eingabeliste. Es dürfen keine Elemente dazukommen oder verloren gehen.

## b)
1. Leere Liste:
   Die Methode sollte auch bei einer leeren Liste keine Fehler werden und eine leere Liste zurückgeben.
2. Einzelnes Element:
   Eine Liste mit nur einem Element muss unverändert zurückgegeben werden.
3. Liste mit identischen Elementen:
   Bei allen Elementen gleich (z.B. bei `[5, 5, 5, 5]`) ist das Sortierergebnis ebenfalls identisch mit der Eingabeliste.
4. Zufällige Reihenfolge:
   Eine Liste in beliebiger Reihenfolge (z.B.`[3, 1, 4, 2]`) soll korrekt in absteigender Reihenfolge sortiert werden `[4, 3, 2, 1]` 
5. Breits sortierte Liste:
   Eine Liste, die in aufsteigender Reihenfolge vorliegt (z.B. `[1, 2, 3, 4]`), muss so umsortiert werden, dass sie in absteigender Reihenfolge vorliegt `[4, 3, 2, 1]`. 

