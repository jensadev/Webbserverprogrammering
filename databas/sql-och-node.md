# SQL och Node

Installera [mysql paketet](https://www.npmjs.com/package/mysql) för node med npm. 

Installera paketen [mysql](https://www.npmjs.com/package/mysql) och [dotenv ](https://www.npmjs.com/package/dotenv)för Node.

```text
npm install mysql
```

* mysql paketet innehåller drivrutiner för att använda mysql med node.
* dotenv är ett paket för att skapa konfiguarationsfiler. Att [spara konfigurationsdata separerat](https://12factor.net/config) från koden är god praxis.

En databas behöver användaruppgifter för att koppla upp sig till en databasserver. Denna data är känslig och du behöver skydda den, därför är det viktigt att inte ladda upp denna data på GitHub. För att underlätta det används paketet [dotenv](https://www.npmjs.com/package/dotenv). Med dotenv kan vi skapa en konfigurationsfil och med hjälpa av .gitignore så säkerställer vi att den inte laddas upp på GitHub.

{% tabs %}
{% tab title="Bash" %}
```bash
npm install mysql dotenv
```
{% endtab %}
{% endtabs %}

## Koppla upp

Detta exempel är plockat från mysql-paketets [hjälpsida](https://www.npmjs.com/package/mysql), den återfinns även i [Express-dokumentation](https://expressjs.com/en/guide/database-integration.html#mysql). Ofta ser Node-hjälp ut på detta sättet och det kan vara svårt att få svar på var detta ska skrivas\(vi kommer till det\). Du behöver inte koda detta, men om du gör det så kan du lägga koden i en route.

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

{% hint style="warning" %}
Node kan få problem med auth till MySql 8. Då behöver du uppdatera din mysql-användare på mysql servern. SQL frågan för detta finns här nedanför.
{% endhint %}

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
FLUSH privileges;
```
{% endtab %}
{% endtabs %}

## Konfiguration

Det första som behöver åtgärdas är konfigurationsvärdena i exemplet och för det så ska vi använda dotenv paketet. En .env fil är en konfigurationsfil\(eller en fil med miljövariabler, environment\),. Filnamnet börjar med en punkt, vilket är en Linux standard för att visa att det rör sig om en dold fil\(vilket ofta konfigurationsfiler är\).

Att arbeta med konfigurationen för ett projekt i en separat fil är en god praxis som låter dig konfigurera upp ett projekt utan att ändra i koden. Att separera konfigurationen skyddar känslig konfigurationsdata\(lösenord, användare api-nycklar\) från att laddas upp på till exempel GitHub. .env filen skapas i projektets root. Skapa först en fil med namnet .env-example utan konfigurationsvärden. Kopiera sedan filen till .env och fyll i konfigurationsvärdena. För en databaskonfiguration behövs, host, username, password och databas.

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

Skapa sedan en .gitignore fil i projektets root för att kontrollera så att .env filen inte laddas upp på GitHub.

{% code title=".gitignore" %}
```bash
# dotenv environment variables file
.env
```
{% endcode %}

{% hint style="danger" %}
Utan .gitignore kvittar det att du skapar .env filerna.
{% endhint %}

För att komma åt värdena från .env filen så behöver paketet laddas in så tidigt som möjligt i applikationen. Värdena återfinns sedan i objektet `process.env`.

{% code title="app.js" %}
```javascript
require('dotenv').config();
```
{% endcode %}

Med dotenv paketet laddat kan det ovanstående exemplet ändras till.

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

För att använda mysql kommer vi att skapa en återanvändbar modell. Denna modell kommer även att använda en [pool ](https://www.npmjs.com/package/mysql#pooling-connections)med uppkopplingar. Modellen kommer att exportera en skapad pool för användning.

Skapa en ny mapp _models_ i projektets root och i models en fil med namn, _db.js_.

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

Skapa sedan en ny route fil för att testa modellen, _routes/test.js._

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

App.js behöver uppdateras för att ladda routen, gör följande ändring där de andra routerna laddas och används.

{% code title="app.js" %}
```javascript
...
const testRouter = require('./routes/test');
...
app.use('/test', testRouter);
```
{% endcode %}

Databasmodellen sparas i variabeln pool för användning. Sedan används metoden `.getConnection()` för att hämta en uppkoppling från poolen. Sidan visar sedan uppkopplingens id innan uppkopplingen släpps, release. Testa vad som sker utan `connection.release()`.

## Databasfråga

För att testa att kommunicera med en databas behöver du starta en mysql-server och importera databasen från [exemplet](databas-exempel.md).

{% hint style="info" %}
Databasdump finns [här](https://raw.githubusercontent.com/jensnti/Webbserverprogrammering/master/exempel/meeps.sql). Använd wget för att hämta den.
{% endhint %}

{% tabs %}
{% tab title="Bash" %}
```bash
wget https://raw.githubusercontent.com/jensnti/Webbserverprogrammering/master/exempel/meeps.sql
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Du kan också använda [Tableplus ](https://tableplus.com/)för att importera databaser.
{% endhint %}

När du laddat ned filen så förbered för att importera den med att skapa en databas\(om det behövs\).

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

Skapa sedan en view som heter _test_. I en första test skriver vi ut värdena från mysql resultatet med [interpolation ](https://pugjs.org/language/interpolation.html)i Pug. Det är då viktigt att se till att värden som skrivs ut [escapas](https://en.wikipedia.org/wiki/Escape_character), detta för att skadlig kod eventuellt kan sparas i en databas och sedan reproduceras för en användare på webbplatsen. 

{% hint style="info" %}
Använd `console.log()` i routen för att felsöka och undersöka data. Detta dumpar datan till den terminal där du startat upp node.
{% endhint %}

Resultatet måste itereras, eftersom det är en array. För att göra det används Pugs each funktion.

{% code title="views/test.pug" %}
```javascript
extends layout

block content
  each row in result
    p #{row.body}
    | #{row.updated_at}
```
{% endcode %}

För att få ut den fullständiga information, med författarens namn så behöver SQL frågan använda en join på users tabellen. Uppdatera koden som följer.

{% code title="routes/test.js" %}
```javascript
const sql = 'SELECT meeps.*, users.name FROM meeps JOIN users ON meeps.user_id = users.id';
```
{% endcode %}

Nästa steg blir sedan att utveckla test-viewen. Detta kan med fördel göras som en mixin. Koden som följer skapar ett kort för en post. 

{% code title="views/test.pug" %}
```javascript
  .card
    .card-body
      p.card-text= result.body
    .card-footer.d-flex
      p.small Posted by 
        a(href="#")= result.name
        |  on 
        time(date="#{updated}")= result.updated_at
```
{% endcode %}

### Övning

* Skapa en mixin som du kan kalla på för varje rad i databasen, antingen skriver du kod för ett eget kort med css eller så använder du ett Bootstrap kort.

## Databas selektion

I SQL så kan WHERE användas för att välja rader utifrån ett logiskt uttryck. För att välja en specifik meeps så används ID kolumnen. Det ser ut som följer med resultat.

{% tabs %}
{% tab title="SQL" %}
```sql
SELECT * FROM meeps WHERE id = 2;
+----+-------------+--------------------------+---------------------+---------------------+---------+
| id | title       | body                     | created_at          | updated_at          | user_id |
+----+-------------+--------------------------+---------------------+---------------------+---------+
|  2 | Hello there | Lorem ipsum dolor sit... | 2020-09-25 13:04:34 | 2020-09-25 13:19:53 |       1 |
+----+-------------+--------------------------+---------------------+---------------------+---------+
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Du kan självklart välja data utifrån andra fält som user\_id för att välja alla poster från en specifik användare. För wildcard så används %.
{% endhint %}

#### Övning

* Prova att skriva en SQL fråga som låter dig söka efter mailadresser i databasen. Tänka att användaren ska kunna söka efter ett förnamn och få tillbaka matchande adresser. Använd SQL wildcards, %.

### Selektion och node

För att kunna välja rader från databasen med specifika ID, så måste servern kunna ta emot ID parametern. För att skicka en parameter behöver en route skapas eller ändras för att tillåta detta. Då används `:PARAMETERNAMN` i routen.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
router.get('/:id', function (req, res, next) {
  // console.log(req.params); hela objeketet
  console.log(req.params.id); // id parameterns värde
});
```
{% endtab %}
{% endtabs %}

Denna route på / läser in en parameter med namnet `:id`. Värdet på parametrarna återfinns i requestens\(req\) parameter-objekt. Du kommer åt detta genom punkt-notation. 

Värdet som skickats med en parameter kan sedan användas i SQL frågan, det görs genom en så kallad **prepared statment**. Prepared statements\(även **parameterized query**\) används av säkerhetsskäl för att undvika [SQL-injektioner](https://imgs.xkcd.com/comics/exploits_of_a_mom.png).

{% code title="routes/test.js" %}
```javascript
router.get('/:id', function (req, res, next) {
  const sql = 'SELECT * FROM meeps WHERE id = ?';

  pool.query(sql, [req.params.id], function (err, result, fields) {
    if (err) throw err;
    res.json({
      status: 200,
      id: req.params.id,
      result: result
    });
  });
});
```
{% endcode %}

Ändringen i test-routen skapar en route för get som tillåter en id parameter. Rad 2 skapar SQL-frågan där frågetecknet är en parameter. Frågan körs sedan på rad 4,  då ersätts ? med \[req.params.id\]. Det går utmärkt att använda flera parameterar\(tänk post med ett formulär\). Med flera parametrar är det viktigt att de är satta i rätt ordning.

Om frågan exekveras korrekt så returneras svaret som json till webbläsaren.

### Övning

Skapa länkar till enskilda meeps.

* test.pug, ge varje meep en länk till /test/:id
* skapa /test/:id routen
* skapa SQL som hämtar vald meep utifrån :id \(inklusive join\)
* skapa en meep.pug views som visar vald meep\(ersätt json i exemplet ovan\)

##  Databasmodell, asynkrona frågor

Ibland så uppstår problem med databasuppkopplingen eller så behövs det flera uppkopplingar. Vid dessa tillfällen så kan du behöva större kontroll över vad som sker och hur data levereras till klienten. En lösning på detta är att göra asynkrona anrop, då kan du styra över att din kod ska invänta svar från databasen.

För att möjliggöra asynkrona anrop ändras databasmodellen till följande.

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

function query(sql, params) {
  return new Promise((resolve, reject) => {
    pool.query(sql, ...params, function (err, result, fields) {
      if (err) reject(err);
      resolve(result);
    });
  });
}

module.exports = { pool, query };

```
{% endcode %}

Query funktionen accepterar en SQL fråga samt tillhörande parametrar. 

### Flera frågor

Nu kan `query` funktionen användas i en route genom att requira den. Med query funktionen kan routen göras asynkron med nyckelordet `async`.

{% hint style="info" %}
Async tillåter användningen av await vilket tillåter koden att invänta svar. Det görs genom något som kallas för promises. En promise resulterar i att den antingen resolves eller rejects, körs eller misslyckas. [MDN async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).
{% endhint %}

I koden nedan så ändras users routen för att tillåta användaren att köra den med en parameter `:id`, användarens id. För att illustrera dubbla SQL-frågor så hämtas då även den användarens alla tillhörande meeps. Utan att använda `async` och `await` så väntar aldrig scriptet på att dessa frågor ska köras, utan returnerar ingen data. Jämför koden här nedan och prova båda versionerna.

{% tabs %}
{% tab title="Sync" %}
{% code title="routes/users.js" %}
```javascript
var express = require('express');
var router = express.Router();
const { query } = require('../models/db');

router.get('/', function (req, res, next) {
  res.render('users', { title: 'Userpage', users: ['Hans', 'Moa', 'Bengt', 'Frans', 'Lisa'] });
});

router.get('/:id', function (req, res, next) {
  try {
    const user = query(
      'SELECT * FROM users WHERE id = ?',
      req.params.id
    );

    const meeps = query(
      'SELECT * FROM meeps WHERE user_id = ?',
      req.params.id
    );

    res.render('users', {
      id: req.params.id,
      user: user,
      meeps: meeps
    });
  } catch (e) {
    console.error(e);
    next(e);
  }
});

module.exports = router;

```
{% endcode %}
{% endtab %}

{% tab title="Async" %}
{% code title="routes/users.js" %}
```javascript
var express = require('express');
var router = express.Router();
const { query } = require('../models/db');

router.get('/', function (req, res, next) {
  res.render('users', { title: 'Userpage', users: ['Hans', 'Moa', 'Bengt', 'Frans', 'Lisa'] });
});

router.get('/:id', async function (req, res, next) {
  try {
    const user = await query(
      'SELECT * FROM users WHERE id = ?',
      req.params.id
    );

    const meeps = await query(
      'SELECT * FROM meeps WHERE user_id = ?',
      req.params.id
    );

    res.render('users', {
      id: req.params.id,
      user: user,
      meeps: meeps
    });
  } catch (e) {
    console.error(e);
    next(e);
  }
});

module.exports = router;

```
{% endcode %}
{% endtab %}

{% tab title="Pug" %}
{% code title="views/users.pug" %}
```markup
extends layout

block head
  -var title = "Användare"
  title Webbserverprogrammering - #{title}

block content
  main
    .container
      h1= title
      if users
        p.lead Här är ett exempel på iteration
        ul.list
          each user in users
            li= user

      if user[0]
        p= user[0].name
        h3 Användarens meeps
        if meeps
          ul.list
            each meep in meeps
              li= meep.body
```
{% endcode %}
{% endtab %}
{% endtabs %}

Koden väntar på att [promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) för båda SQL-frågorna ska bli "klar". När den är klar så renderas users-view med databas-frågornas data. Frågorna körs i ett `try` block för att fånga eventuella fel med `catch`. Om ett fel uppstår så fångas det upp och vi använder Express inbyggda [felhanterare](https://expressjs.com/en/guide/error-handling.html), `next(error)`.

I användar-vyn så används selektion i Pug-koden för att det inte ska visas fel när data saknas\(testa att köra utan if-satserna\). 

{% hint style="info" %}
[Koden för exempel-projektet finner du här i DB grenen.](https://github.com/jensnti/wsp1-node/tree/db)
{% endhint %}

{% hint style="info" %}
[Samtliga moment i detta kapitel finns visade i denna speedrun.](https://youtu.be/BnweeiRfE_U)
{% endhint %}

