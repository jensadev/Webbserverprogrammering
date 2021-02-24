# Introduktion

## Validering

Att kontrollera att data är korrekt.

### Klient

I webbläsaren, genom formulär och javascript.

### Server

Kontrollera innehållet i data, dess typ.

## Sanera, tvätta

Att tvätta data så att den inte innehåller något skadligt. Detta kan vara att ta bort specialtecken.

### Escapa

En metod som är vanligt förekommande är att escapa specialtecken, så att kod inte är skadligt. Det görs ofta genom att det sätts in tecken före som stoppar tecknet från att köras.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
backslashisträng = "Detta \"är en quote\" i strängen."
```
{% endtab %}
{% endtabs %}

## SQL-injektioner

Injektioner är en typ av attack som sker på datorer där data injiceras, stopppas in, där den inte ska vara. Ett exempel på detta är att skadlig data stoppas in i en databas för att få den att göra något annat, som till exempel att köra flera SQL frågor än utvecklaren tänkt. Ett annat exempel kan vara att kod injiceras i en DLL fil, som gör att du kan se genom väggar i ett spel.

![https://xkcd.com/327/](../.gitbook/assets/exploits_of_a_mom.png)

Läs mer, [https://en.wikipedia.org/wiki/SQL\_injection](https://en.wikipedia.org/wiki/SQL_injection)

## Cross Site Scripting\(XSS\)

Att på ett eller annat sätt injicera skadligt javascript i en databas eller annan kod som körs, så att det körs på en annan klient.

Läs mer, [https://en.wikipedia.org/wiki/Cross-site\_scripting](https://en.wikipedia.org/wiki/Cross-site_scripting)

### Self XSS

Ofta luras en användare att "köra kod" i consolen på webbläsaren för att få någon slags fördel, det kan vara att "logga in" eller läsa någon annans meddelanden. Resultatet är alltid att användaren utsätter sig själv för en XSS attack och blir offret.

{% hint style="info" %}
Öppna consolen på [facebook.com](https://facebook.com).
{% endhint %}

## Cross Site Request Forgery\(CSRF\)

Denna attack går ut på att stjäla en annan användares cookie/session för att sedan låtsas vara den användaren för att kunna utföra sådant som en inte egentligen skulle kunna göra.

Läs mer, [https://en.wikipedia.org/wiki/Cross-site\_request\_forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

