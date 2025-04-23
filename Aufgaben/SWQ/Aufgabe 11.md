
Erstellt: 09.04.2025 15:31

Tags:[[Software Qualität]]

# Aufgabe 11


## a)
```java
public class Main {  
    public static void main(String[] args) {  
  
    }  
  
    public static void methode() {  
        System.out.println("Ich werde nicht aufgerufen");  
    }  
}
```
Die Methode `methode()` wird niemals aufgerufen und fällt deswegen bei der C0 Überdeckung raus.

## b)
```java
public class Main {
	public static void main(String[] args) {
		pruefe(true);
	}

	public static void pruefe(boolean bedingung) {
		if(bedingung) {
			System.out.println("Bedingung ist wahr!!1!1!1ELF");
		} else {
			System.out.println("Bedingung ist falsch OMG");
		}
	}
}
```
Die Methode wird zwar aufgerufen aber immer nur mit true als Bedingung. Der else-Zweig wird nie aufgerufen.

## c)

```Java
public class Main {  
    public static void main(String[] args) {  
        pruefe(true, false);
    }  
  
    public static void pruefe(boolean a, boolean b) {  
        if(a && b) {  
            System.out.println("Beide bedinungen sind wahr");  
        } else {  
            System.out.println("Mindestens eine Bedingung ist falsch");  
        }  
    }  
}
```
Die Methode hat eine C1-Überdeckung, da in der if-Anweisung sowohl true als auch false abgedeckt wird. Aber für C2 ist es erforderlich, dass alle drei Möglichkeiten der Parameter, die in Frage kommen, abgedeckt sind. Also:
1. `a = true, b = true`
2. `a = false, b = true`
3. `a = true, b = false`

## d)

### MC/DC Erklärung
- MC/DC ist eine Testabdeckungsmethode, die in sicherheitskritischen Anwendungen verwendet wird.
- sie zielt darauf ab, sicherzustellen, dass jede einzelne Bedingung in einer Entscheidung unabhängig voneinander das Ergebnis der Entscheidung beeinflussen kann.
- Für jede Bedingung müssen Testfälle existieren, bei denen:
	- Die Bedingung ihren Wert ändert (von true zu false oder umgekehrt).
	- Alle anderen Bedingungen konstant bleiben.
	- Das Ergebnis der Entscheidung sich ändert.

### Beispiel mit der Methode

1. `a = true, b = true, c = false -> true`
2. `a = true, b = false, c = true -> true`
3. `a = true, b = false, c = false -> false`
4. `a = false, b = true, c = false -> false`

## e)

- Die Methoden istDisjunktMit(), schneidenMit() und istIntervall() prüfen nicht auf null. Es kommt also zu errors.
- Nicht alle Eventualitäten werden untersucht, daher kann ist die Coverage auch nicht immer auf 100%