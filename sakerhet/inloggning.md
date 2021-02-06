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
{% tab title="JavaScript" %}
{% code title="package.json" %}
```javascript
"start": "nodemon ./bin/www"
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% embed url="https://github.com/jensnti/wsp1-login" %}

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



### Kom ihåg mig

Databasen behöver en token som även sparas hos användaren, så att en "ihågkommen" inloggning kan kontrolleras.

## Säkerhet

