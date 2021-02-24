# Testdriven utveckling

## Utvecklingsmetod

Applikationer kan utvecklas på många sätt. Att arbeta test drivet\(engelska, Test Driven Development, **TDD**\) är ett arbetssätt som är väldigt populärt. Arbetssättet går ut på att skriva tester först, för att sedan skriva koden som löser testet.

Testet anger alltså vad koden ska göra, vilket gör att koden alltid kan testas och funktionen kan garanteras så länge testet fungerar.

Om testet fungerar så kallas det att testet är grönt, fungera det inte så är det rött. Detta har i sin tur skapat arbetsmetoden rött, grönt, refaktorisera\(engelska, red, green, refactor\).

* Skriv test
* Kör test, rött
* Skriv koden som löser testet, grönt
* Refaktorisera koden för att öka kodkvaliten

Metoden bygger mycket på att kod som löser ett test måste inte nödvändigtvis vara bra eller "snygg". Det kan helt enkelt bara lösa problemet och det är ofta så att det första utkastet av koden inte heller är bra. Att refaktorisera koden ger alltså en mycket mer robustkodbas.

Testen finns sedan kvar för att kunna köras så snart koden har ändrats. Gröna test blir också en förutsättning för att kod ska kunna mergas till Main.

