Tags: [[Theoretische Informatik]], [[Theoretische Informatik Entscheidbarkeit]]

### Definition
*konkatenieren = aneinanderhängen*
Seien wort1 und wort2 zwei [Worte](remote-data/Zettelkasten/Theoretische%20Informatik/Wort.md) über einem [[Alphabet]], dann entsteht die *Konkatenation*, geschrieben ° , der Wörter durch ein Anhängen von wort2 an wort1, genauer
$$°: Alphabet^{*} \times Alphabet^{*} \rightarrow Alphabet^{*}$$
- wort1 = a1...an     wort2 = b1...bn     dann wort1 ° wort2 = a1...anb1...bn
- ab ° ba = abba     a ° bba = abba     a ° $\epsilon$ = $\epsilon$ ° a = a

- ==Randnotiz:== Da jedes Zeichen eines Alphabets auch ein Wort der Länge 1 ist, besteht jedes Wort aus der Konkatenation seiner Zeichen.
- Wenn es klar ist, dass es um eine Konkatenation geht, wird das Konkateantionszeichen einfach weggelassen, also statt    wort1 ° wort2     dann     wort1wort2


##### weitere Operationen:
[[Weitere Operationen auf Wörtern]]
