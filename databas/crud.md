---
description: 'Create, read, update, delete'
---

# CRUD

CRUD är de fyra grundläggande funktionera för att arbeta med data\(create, read, update, delete\). Det är inte kopplat till webben eller databas även om det är väldigt vanligt i det sammanhanget. Konceptet är att data ska kunna skapas, läsas, redigeras och tas bort.

Det som följer här nedan är ett exempel på CRUD. Exemplet är skapat i samma kod som används för [inloggning](../sakerhet/inloggning.md). Inloggningen är dock inget krav och det går utmärkt att skapa detta utan en inloggning. Du behöver då ersätta user fältet med användarens namn, eller skippa det helt.

{% hint style="danger" %}
Notera att koden i detta avsnitt kan kräva att du ändrar din databasmodell till att inte använda ..params
{% endhint %}

## Router

Skapa en router för meeps, detta kommer hålla huvuddelen av arbetet. Detta kan implementeras vid sidan av login systemet, sedan kan funktionalitet flyttas till index eller så.

Routern kommer att behöva följande delar, börjar med att skapa logiken direkt i routern, men det kommer bli ganska mycket kod så det rekommenderas att du flyttar det du kan till en controller. 

* show, visa alla resurser
* show/:id, visa en resurs med :id
* create, skapa en resurs
  * GET visar ett formulär
  * POST skapar resursen
* update/:id, uppdatera en resurs med :id
  * GET visar ett formulär
  * POST uppdaterar resursen
* delete, ta bort en resurs

De router som låter användaren manipulera en resurs behöver verifiera att användaren är inloggad. Grunden till det finns i login systemet. För att förenkla inloggningskontrollen ska  vi skapa en middleware för att sköta det.

{% hint style="info" %}
Om du inte använder inloggningen så behöver du inte heller verifiera en användare. Systemet är då oskyddat, men det går utmärkt att testa.
{% endhint %}

### Verify, middleware

I nuläget så kontrolleras användarens session på varje ställe där inloggning krävs med en if sats, det är både osäkert, besvärligt och svårt att hantera. Koden för att bekräfta användarens session kan flyttas till en middleware\(ett tillägg som kan kallas på i express\). Detta middleware, som vi kallar för verify, anropas sedan på i de router som ska verifieras.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/home.route.js" %}
```javascript
// session kontroll med if sats i routen
router.get('/', (req, res, next) =>{
  if (req.session.loggedin) {
    res.render('home', {
      username: req.session.username
    });
  } else {
    res.redirect('/login');
  }
});
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="routes/home.route.js" %}
```javascript
...
const { verify } = require('../middlewares/verify');
// uppdaterad route med middleware, notera att verify kallas på innan
// den resolvas
router.get('/', verify, (req, res, next) => {
    res.render('home', {
      username: req.session.username
    });
});
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="middlewares/verify.js" %}
```javascript
// kontrollerar om det finns en session och om anv. är inloggad
exports.verify = (req, res, next) => {
  const session = req.session;
  if (!session) {
    res.status(401).redirect('/login');
  } else {
    if (session.loggedin) {
      next();
    } else {
      res.status(401).redirect('/login');
    }
  }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Read, GET routes

Ofta påbörjas arbetet med CRUD genom att kunna skriva ut en resurs, det vill säga att läsa den. Då får resursen skapas manuellt i databasen. Designen för resursen i det här fallet bygger på tidigare kapitel i boken. Det krävs två tabeller för resurserna, meeps och users då meeps innehåller en relation till users tabellen.

{% hint style="info" %}
Skapa tabellerna och testdata innan du går vidare.
{% endhint %}

{% tabs %}
{% tab title="SQL" %}
```sql
mysql> describe meeps;
+------------+-----------------+------+-----+---------+----------------+
| Field      | Type            | Null | Key | Default | Extra          |
+------------+-----------------+------+-----+---------+----------------+
| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| user_id    | bigint unsigned | NO   |     | NULL    |                |
| body       | text            | NO   |     | NULL    |                |
| created_at | timestamp       | YES  |     | NULL    |                |
| updated_at | timestamp       | YES  |     | NULL    |                |
+------------+-----------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)
```
{% endtab %}

{% tab title="SQL" %}
```sql
mysql> describe users;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int unsigned | NO   | PRI | NULL    | auto_increment |
| name       | varchar(50)  | NO   | UNI | NULL    |                |
| password   | varchar(255) | NO   |     | NULL    |                |
| email      | varchar(255) | NO   | UNI | NULL    |                |
| created_at | timestamp    | NO   |     | NULL    |                |
| updated_at | timestamp    | NO   |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
6 rows in set (0.02 sec)
```
{% endtab %}
{% endtabs %}

Routerna för att läsa resurser ser ut som följer. Det handlar först och främst om att hämta resurser med SQL. Frågan använder JOIN för att hämta användarens namn från users tabellen. Utskriften behöver skapas med en template och kallas på genom res.render.

{% hint style="info" %}
Skippar du användartabellen så lägger du till author direkt i meeps. Du behöver då inte använda JOIN.
{% endhint %}

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* GET all meeps */
router.get('/',
  async (req, res, next) => {
    const sql = 'SELECT meeps.*, users.name AS author FROM meeps JOIN users ON meeps.user_id = users.id';
    result = await query(sql);
    res.send(result);
});

/* GET a meep */
router.get('/:id',
  param('id').isInt(),
  async (req, res, next) => {
    const sql = 'SELECT meeps.*, users.name AS author FROM meeps JOIN users ON meeps.user_id = users.id WHERE meeps.id = ?';
    result = await query(sql, req.params.id);
    res.send(result);
});
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// UTAN USERS
/* GET all meeps */
router.get('/',
  async (req, res, next) => {
    const sql = 'SELECT * FROM meeps';
    result = await query(sql);
    res.send(result);
});

/* GET a meep */
router.get('/:id',
  param('id').isInt(),
  async (req, res, next) => {
    const sql = 'SELECT * FROM meeps WHERE meeps.id = ?';
    result = await query(sql, req.params.id);
    res.send(result);
});
```
{% endtab %}
{% endtabs %}

Glöm inte att registrera routen i appen.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="app.js" %}
```javascript
...
const meepsRouter = require('./routes/meeps.route');
...
app.use('/meeps', meepsRouter);
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Create, GET och POST routes

Dessa routes kommer att använda verify middlewaren för att autentisera användaren. Routerna kommer även att behöva validera och tvätta input för att säkra systemet.

För att skapa nya resurser används ett webbformulär, routen för att nå formuläret kan till exempel vara /new. Det är viktigt att denna route kommer före :id routen\(i koden\) för att hämta enskilda resurser, annars kommer det inte att fungera.

{% hint style="info" %}
Router som läser parametrar med :PARAM behöver komma efter de andra i ordningen. Det som sker annars är att /new hamnar i /:id parametern.
{% endhint %}

Själva formuläret kan med fördel skrivas som en komponent och inkluderas på andra sidor för att skapa nya resurser.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* GET a form for posting a new meep  */
router.get('/new',
  verify,
  (req, res, next) => {
    res.render('meepsform');
});
```
{% endcode %}
{% endtab %}

{% tab title="PUG" %}
{% code title="views/meepsform.pug" %}
```javascript
extends layout 

block content 
  form(action="/meeps", method="post") 
    textarea#body(name="body", cols="30", rows="10") 
    button(type="submit") Post
```
{% endcode %}
{% endtab %}
{% endtabs %}

Formuläret kommer att posta resursen till /meeps där POST routen hanterar det.

{% hint style="danger" %}
Koden är osäker och hanterar inte XSS, testa genom att skriva html kod i en meep.
{% endhint %}

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* POST a new meep */
router.post('/',
  body('body').notEmpty(),
  verify,
  async (req, res, next) => {
    const sql = 'INSERT INTO meeps (user_id, body, created_at, updated_at) VALUES (?, ?, now(), now())';
    const result = await query(sql, [req.session.userid, req.body.body]);
    if (result.insertId > 0) {
      res.redirect('/meeps/' + result.insertId);
    }
});

```
{% endcode %}
{% endtab %}
{% endtabs %}

För att kontrollera att en resurs skapats så kontrollerar vi att SQL frågans resultat innehåller ett nytt id. När resursen skapats så skickas användaren vidare till GET routen för att visa en resurs med :id.

{% hint style="info" %}
Utan user\_id med en author så sparar du inte det i databasen utan author. Du använder inte heller då req.session.userid
{% endhint %}

## Update, GET och POST routes

För att uppdatera en resurs används resursens id för att hitta den. Det finns ett antal sätt att skicka med id värdet på. I det här exemplet kommer formuläret innehålla resursens id, värdet skickas i en input med typen hidden.

För att komma åt id värdet skapas en GET update route som tar id värdet som en parameter. Värdet läses in och skrivs ut i formuläret.

Resursens id används för att hämta resursen från databasen. Resursen värden behövs så att den faktiskt kan uppdateras, dessa skickas med res.render för att kunna användas på formulärsidan.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* GET form for updating a meep  */
router.get('/update/:id',
  param('id').isInt(),
  verify,
  async (req, res, next) => {
    const sql = 'SELECT meeps.*, users.name FROM meeps JOIN users ON meeps.user_id = users.id WHERE meeps.id = ?';
    result = await query(sql, req.params.id);
    res.render('meepsform', { meep: result[0] });
});
```
{% endcode %}
{% endtab %}

{% tab title="Pug" %}
{% code title="views/meepsform.pug" %}
```javascript
extends layout 

block content 
  if meep 
    form(action="/meeps/update", method="post") 
      input(type="hidden", name="meepid" value= meep.id)
      textarea#body(name="body", cols="30", rows="10")= meep.body
      button(type="submit") Update meep
  else
    form(action="/meeps", method="post") 
      textarea#body(name="body", cols="30", rows="10") 
      button(type="submit") Post meep
```
{% endcode %}
{% endtab %}
{% endtabs %}

Nedan följer den sista delen, post routen till update som hanterar den faktiska uppdateringen av resursen. Routen behöver fortfarande säkras, validera och tvätta data samt hantera fel.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* POST a new meep */
router.post('/update',
  body('meepid').isInt(),
  body('body').notEmpty(),
  verify,
  async (req, res, next) => {
    const sql = 'UPDATE meeps SET body = ?, updated_at = now() WHERE id = ?';
    const result = await query(sql, [req.body.body, req.body.meepid]);
    if (result.changedRows > 0) {
      res.redirect('/meeps/' + req.body.meepid);
    }
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

## DELETE, POST route

Här presenteras enbart en POST route för att ta bort en resurs. Denna kan länkas från en lista av resurserna eller liknande. Det kan såklart vara bra att låta användaren bekräfta borttagningen av en resurs, då kan javascript används på klient-sidan eller så skapas en GET route för delete, som i sin tur pekar till POST routen.

Alternativt är en GET route till /delete/:id också en möjlighet för att ta bort resurser.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* POST to delete a meep */
router.post('/delete/',
  body('meepid').isInt(),
  verify,
  async (req, res, next) => {
    const sql = 'DELETE FROM meeps WHERE id = ?';
    const result = await query(sql, req.body.meepid);
    if (result.affectedRows > 0) {
      res.redirect('/meeps');
    }
  });
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
...
// Uppdatering av GET / för att visa alla meeps med en meeps.pug template
res.render('meeps', { meeps: result });
...
```
{% endcode %}
{% endtab %}

{% tab title="Pug" %}
{% code title="views/meeps.pug" %}
```javascript
extends layout 

block content 

  each meep in meeps 
    div
      p= meep.body 
      form(action="/meeps/delete", method="post") 
        input(type="hidden", name="meepid" value= meep.id)
        button(type="submit") X
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Summering

Ovan finns alla delarna för att utföra CRUD operationer på en resurs, i detta fall meeps. Systemet kräver fortfarande mycket arbete, med både säkerhet och användbarhet, men grunden är där.

Den kod som presenteras på sidan hör till största del till routes filen för resursen. Den koden bör landa på ett 100-tal rader utan säkerhet och felhantering. Det är hanterbart, men du bör definitivt flytta delar till en controller om du bygger vidare på systemet.



