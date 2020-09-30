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

För att komma åt värdena från .env filen så behöver paketet laddas in så tidigt som möjligt i applikationen. De återfinns sedan i process.env.

{% code title="app.js" %}
```javascript
...
require('dotenv').config();
```
{% endcode %}

Med dotenv blir exemplet med uppkopplingen följande.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const connection = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASS,
  database: process.env.DB_DATABASE
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

## Databasmodell

För att använda mysql kommer vi att skapa en återanvändbar modell. Denna modell kommer även att använda en pool med uppkopplingar. Modellen kommer att exportera en skapad pool för användning.

Skapa en ny mapp _models_ i projektets root och i den, _db.js_.

{% code title="models/db.js" %}
```javascript
const mysql = require('mysql');

const pool = mysql.createPool({
  connectionLimit: 10,
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASS,
  database: process.env.DB_DATABASE
});

module.exports = pool;

```
{% endcode %}

För att testa modellen skapar vi en testroute och tillhörande filer. Skapa _routes/test.js._

{% code title="app.js" %}
```javascript
...
const testRouter = require('./routes/test');
...
app.use('/test', testRouter);
```
{% endcode %}

{% code title="routes/test.js" %}
```javascript
const express = require('express');
const router = express.Router();
const pool = require('../models/db');

pool.getConnection((error, connection) => {
  if (error) {
    throw error;
  }

  res.send('connected as id ' + connection.threadId);

  connection.release();
});

module.exports = router;
```
{% endcode %}

Databasmodellen sparas i variabeln pool för användning. Sedan används metoden `.getConnection()` för att hämta en uppkoppling från poolen. Sidan visar sedan uppkopplingens id innan uppkopplingen släpps, release. Testa vad som sker utan `connection.release()`.

## Databasfråga

Innan du går vidare så behöver du starta mysql-servern och importera databasen från [exemplet](databas-exempel.md).

{% hint style="info" %}
Databasdump finns [här](https://raw.githubusercontent.com/jensnti/Webbserverprogrammering/master/exempel/meeps.sql).
{% endhint %}

{% tabs %}
{% tab title="Bash" %}
```bash
sudo service mysql restart
mysql -u USERNAME -p DATABASE < FILENAME
```
{% endtab %}
{% endtabs %}

Ändra sedan test-routen till att innehålla en faktisk SQL fråga. Routen kommer nu att svara på /test och svara med resultatet av databasfrågan i JSON. Du kan även logga\(console.log\) resulatet från databasfrågan för att se hur objektet ser ut\(det skrivs då i terminalen där du startat node\).

{% code title="routes/test.js" %}
```javascript
router.get('/', function (req, res, next) {
  const sql = 'SELECT * FROM meeps';

  pool.query(sql, function (err, result, fields) {
    if (err) throw err;
    res.json({
      status: 200,
      result
    });
  });
});
```
{% endcode %}

## Databasresultat till en view

I det här steget ska vi skapa en view som kan visa resultatet av en SQL fråga. Detta för att sammankoppla alla delarna. För att göra detta så behöver routen ändras. Test-routens respons ska använda render metoden med en test-view och resultatets data.

{% code title="routes/test.js" %}
```javascript
router.get('/', function (req, res, next) {
  const sql = 'SELECT * FROM meeps';
  
  pool.query(sql, function (err, result, fields) {
    if (err) throw err;
    
    res.render('test', { result: result });
  });
});
```
{% endcode %}







Skapa en view som heter _test_. I en första test skriver vi ut värdena från mysql resultatet med [interpolation ](https://pugjs.org/language/interpolation.html)i Pug. Det är då viktigt att se till att värden som skrivs ut escapas\(säkerhetsrisk\).

{% code title="views/test.pug" %}
```javascript
extends layout

block content
  each row in result
    
```
{% endcode %}



