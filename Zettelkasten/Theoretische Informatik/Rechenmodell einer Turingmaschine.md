Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]

+ es gibt ein unendliches Band von Speicherzellen, die jeweils ein Zeichen aufnehmen können, leere Zellen werden mit einem speziellen Zeichen gekennzeichnet. Am Anfang steht ein beliebiges Wort in den Anfangszellen des Bandes

![[Screenshot 2025-03-17 154952.png | Seite 36]]

+ Es gibt einen Schreib-Lese-Kopf, der immer auf genau einer Zelle steht. In einem Arbeitsschritt wird der Inhalt der Zelle gelesen und dann eine der drei folgenden Aktionen ausgeführt, schreibe ein (nicht notwendigerweise neues) Zeichen und 
	+ gehe nach links
	+ gehe nach rechts
	+ stoppe (beende) die Ausführung
+ Um den nächsten Schritt zu berechnen hat die Turingmaschine einen aktuellen Zustand, in dem das Zeichen gelesen und dann eine der Aktionen ausgeführt wird, definiert in einer Überführungsfunktion
+ **Rahmenbedingungen**
	+ es gibt einen Startzustand
	+ der Schreib-Lesekopf steht am Anfang eine Zelle hinter dem Wort