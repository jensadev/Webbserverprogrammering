# SQL och Node

Installera paketen [mysql](https://www.npmjs.com/package/mysql) och [dotenv ](https://www.npmjs.com/package/dotenv)för Node.

* mysql paketet innehåller drivrutiner för att använda mysql med node.
* dotenv är ett paket för att skapa konfiguarationsfiler. Att [spara konfigurationsdata separerat](https://12factor.net/config) från koden är god praxis.

{% tabs %}
{% tab title="Bash" %}
```bash
npm install mysql dotenv
```
{% endtab %}
{% endtabs %}

## Koppla upp

Detta exempel är plockat från mysql-paketets [hjälpsida](https://www.npmjs.com/package/mysql), den återfinns även i [Express-dokumentation](https://expressjs.com/en/guide/database-integration.html#mysql). Ofta ser Node-hjälp ut på detta sättet och det kan vara svårt att få svar på var detta ska skrivas\(vi kommer till det\).

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const mysql = require('mysql');
const connection = mysql.createConnection({
  host     : 'example.org',
  user     : 'bob',
  password : 'secret'
});
 
connection.connect(function (err) {
  if (err) {
    console.error('error connecting: ' + err.stack);
    return;
  }
 
  console.log('connected as id ' + connection.threadId);
});
```
{% endtab %}
{% endtabs %}

Först skapas en const för mysql-paketet, drivrutinen krävs. Efter det så skapas en uppkoppling till mysql-servern utifrån den angivna konfigurationen. Därefter testats uppkopplingen med connect metoden och vid eventuellt fel så loggas det, annars så loggas uppkopplingen. Koden kan testas i app.js eller i en route fil för express om så önskas.

## Konfiguration

Det första som behöver åtgärdas är konfigurationsvärdena i exemplet och för det så ska vi använda dotenv paketet. En .env fil är en konfigurationsfil\(eller en fil med miljövariabler, environment\), att filnamnet börjar med en punkt är en Linux standard för att visa att det rör sig om en dold fil.

Att arbeta med konfigurationen för ett projekt i en separat fil är en god praxis som låter dig konfigurera upp ett projekt utan att ändra i koden. Det låter oss dessutom skydda känslig konfigurationsdata från att laddas upp på till exempel GitHub. Filen skapas i projektets root, först som .env-example utan konfigurationsvärden. Kopiera sedan filen till .env och fyll i konfigurationsvärdena. För en databaskonfiguration behövs, host, username, password och databas.

{% code title=".env-example" %}
```bash
DB_HOST=
DB_USER=
DB_PASS=
DB_DATABASE=
```
{% endcode %}

{% code title=".env" %}
```bash
DB_HOST=127.0.0.1
DB_USER=username
DB_PASS=password
DB_DATABASE=databasename
```
{% endcode %}

Sist men inte minst kontrollerar du\(eller skapar\) en .gitignore i projektets root för att kontrollera så att .env filen inte laddas upp på GitHub.

{% code title=".gitignore" %}
```bash
# dotenv environment variables file
.env
```
{% endcode %}

## Databasmodell

För att kommunicera med databasen skapar vi en databas modell. Det är i stort sett 

