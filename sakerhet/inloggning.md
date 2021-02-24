# Inloggning

## Teori

Ett exempel på ett inloggningsförfarande genom webbplats.

![](../.gitbook/assets/inlog.sv.png)

Den högra delen är extra information kring säkerhet.

## Praktik

Hur ska vi utföra detta.

Node, express och mysql, se tidigare kapitel.

{% tabs %}
{% tab title="Bash" %}
```bash
mkdir projektnamn
cd projektnamn
express -v pug -c sass --git
npm install
npm install dotenv mysql
npm install nodemon --save-dev
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JSON" %}
{% code title="package.json" %}
```javascript
"start": "nodemon ./bin/www"
```
{% endcode %}
{% endtab %}
{% endtabs %}

Du behöver skapa en route för login, med en tillgörande view. Denna view ska visa ett formulär. Ett formulär består av form taggen, denna kräver en metod\(GET eller POST\) samt en action. Form elementets action attribut bestämmer vars formuläret skickar data när det skickas\(submit\).

I form elementet så använder vi olika former av input element för att hantera data från användaren. Här kan en första validering utföras för att öka säkerheten, som typ av fält.

{% tabs %}
{% tab title="HTML" %}
```markup
<form method="post" action="/login">
    <input type="text" name="username">
    <input type="password" name="password">
    <button type="submit">Login</button>
</form>
```
{% endtab %}
{% endtabs %}

Undersök den data som skickas från ett HTML formulär i webbläsarens utvecklarverktyg. Du hittar denna request under Network fliken.

{% hint style="info" %}
Ostylade HTML formulär är hemska, så hitta gärna något ramverk du kan använda.
{% endhint %}

### Tabell för användare

En användartabell behöver inte så mycket för att fungera. I ett mer utökat system så kan vi behöva spara information för email-verifiering och så vidare. Det är god praxis att spara timestamps kopplat till kontot av säkerhetsskäl.

Notera att både användarnamn och email är unika.

{% tabs %}
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
```
{% endtab %}
{% endtabs %}

### Prata med databasen

Sker som tidigare med databasmodellen från tidigare kapitel.

{% page-ref page="../databas/sql-och-node.md" %}

### Ta emot data

Din login route behöver ta emot och hantera en post request från login formuläret. Den data som routen tar emot sparas i req.body.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/login.js" %}
```javascript
router.post('/', async function(req, res, next) {
  console.log(req.body);
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Lösenord

Användaren kommer att skicka sitt lösenord i klartext till din server\(ett problem med HTTP som åtgärdats med HTTPS\). Det lösenordet är något som vi aldrig ska spara av säkerhetsskäl.

{% hint style="danger" %}
Spara aldrig lösenord i klartext i databasen.
{% endhint %}

Det som sparas i databasen ska vara en **hash**, det vill säga en sträng med olika tecken. För att hasha lösenordet används en algoritm som kallas för [bcrypt](https://en.wikipedia.org/wiki/Bcrypt). Bcrypt anses vara säker för detta.

Bcrypt finns som ett node paket.

```text
npm install bcrypt
```

Förfarandet blir sedan att.

1. Ta emot användarens lösenord.
2. Omvandla lösenordet till en hash.
3. Hämta den sparade hashen av lösenordet från databasen.
4. Jämföra de två, om det stämmer loggas användaren in.

Bcrypt paketet använder ett par metoder för det, se [manualen](https://www.npmjs.com/package/bcrypt#usage).

### Kom ihåg att jag är inloggad

För detta så använder du sessions eller kakor.

{% tabs %}
{% tab title="Bash" %}
```bash
npm install express-session
```
{% endtab %}
{% endtabs %}

Konfigurationsanvisningar finns på paketets sida, [npmjs.org](https://www.npmjs.com/package/express-session). För att sätta en session parameter så används request objeketet i express.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
req.session.loggedin = true;

if (req.session.loggedin) // inloggad!
```
{% endtab %}
{% endtabs %}

## Create, read, update, delete

Ett login-system innehåller oftast alla delar av create, read, update, delete\(**CRUD**\). [CRUD ](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)är en akronym för de vanliga operationerna som utförs när data sparas, i ett login-system blir det.

* Skapa en användare, create.
* Logga in en användare, read.
* Uppdatera en användare, update.
* Ta bort en användare, delete.

### SQL

CRUD kan kopplas till ett antal SQL frågor som utför detta. 

{% tabs %}
{% tab title="SQL" %}
```sql
SELECT password FROM users WHERE name = name;
INSERT INTO users (name, password) VALUES ('namn', 'pass');
UPDATE users SET namn = 'namnet' WHERE id = 1;
DELETE FROM users WHERE id = 1;
```
{% endtab %}
{% endtabs %}

Alla frågorna kan köras genom databasmodellen vi använt och värden bör används med förberedda frågor, det vill säga använda platshållare ? för värden.

{% hint style="info" %}
CRUD rör inte bara databas, utan handlar om att spara data på något sätt i en applikation.
{% endhint %}

## Övningsprojekt

* Skapa ett login system.
* Låt användaren skriva korta bloggar/inlägg/tweets eller vad det nu kan vara.
  * Databasdesignen finns [här](../databas/databasdesign.md).
* Gör så att en användares inlägg visas på deras "hem" sida.
* Andra användare, som är inloggade, ska kunna läsas andras "hem".
* Skapa funktionen för att kommentera på andras inlägg.
  * Bra övning i databasdesign och JOIN i SQL.

## Repo

Kopiera inte slutprodukten, då lär du dig inget, gå igenom de commits som är gjorda för att se vad du har missat och kan behöva.

{% embed url="https://github.com/jensnti/te-18login/commits/main" %}





