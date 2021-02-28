---
description: 'Create, read, update, delete'
---

# CRUD

Ett mer komplett exempel, meeps + login.

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

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/meeps.route.js" %}
```javascript
/* GET all meeps */
router.get('/',
  async (req, res, next) => {
    const sql = 'SELECT meeps.*, users.name FROM meeps JOIN users ON meeps.user_id = users.id';
    result = await query(sql);
    res.send(result);
});

/* GET a meep */
router.get('/:id',
  param('id').isInt(),
  async (req, res, next) => {
    const sql = 'SELECT meeps.*, users.name FROM meeps JOIN users ON meeps.user_id = users.id WHERE meeps.id = ?';
    result = await query(sql, req.params.id);
    res.send(result);
});
```
{% endcode %}
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

Du kan nu skapa data i databasen och testa.

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
{% endtabs %}

## Create, GET och POST routes

Dessa routes kommer att använda verify middlewaren för att autentisera användaren. Routerna kommer även att behöva validera och tvätta input för att säkra systemet.

För att skapa nya meeps används ett formulär, routen för formuläret kommer att vara /new. Det är viktigt att denna route kommer före :id routen\(i koden\) för att hämta enstaka meeps, annars kommer det inte att fungera.

Själva formuläret kan med fördel skrivas som en komponent och används på visa meeps sidan, home sidan eller en new sida.

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

Formuläret kommer att posta data till /meeps där POST routen hanterar det.

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

## Update, GET och POST routes

För att uppdatera en resurs används resursens id. Det finns ett antal sätt att skicka med detta på. I det här exemplet kommer meeps formuläret kunna innehålla resursens id för uppdatering. För att göra detta så skapas en get route för update som kräver ett id.

Resursens id används för att hämta det från databasen så att det faktiskt kan uppdateras, detta skickas med resolve för att visas på formulärsidan. Formuläret uppdateras för detta och använder då den existerande resursens värden för att fylla i det.

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

Post routen som hanterar uppdatering av resursen. Routen behöver fortfarande säkras, validera och tvätta data samt hantera fel.

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

Här presenteras enbart en POST route för att ta bort en resurs. Denna kan länkas från en lista av meeps eller liknande. Det kan såklart vara bra att låta användaren bekräfta borttagningen av en resurs, då kan javascript används på klient-sidan eller så skapas en GET route för delete, som i sin tur pekar till POST routen.

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

Ovan finns alla delarna för att utföra CRUD operationer på en resurs, i detta fall meeps. Systemet kräver fortfarande mycket arbete, med både säkerhet och användbarhet.



