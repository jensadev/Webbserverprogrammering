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

## Cross Site Scripting\(XSS\)



## Cross Site Request Forgery\(CSRF\)



