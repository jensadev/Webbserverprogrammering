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

{% embed url="https://github.com/jensnti/wsp1-login" %}

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



### Kom ihåg mig

Databasen behöver en token som även sparas hos användaren, så att en "ihågkommen" inloggning kan kontrolleras.

## Säkerhet

