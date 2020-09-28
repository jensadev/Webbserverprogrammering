# Ett exempel

För att omsätta teorin från [SQL ](sql.md)avsnittet kommer vi nu att skapa en två tabeller och välja data ur dem. Exemplet kommer vara en tabell för användare, users, och en för kortare meddelanden. Tabellerna kommer att länkas med användarens id.

Det första som behöver skapas är en databas.

{% tabs %}
{% tab title="SQL" %}
```sql
 CREATE DATABASE exempel CHARACTER SET UTF8mb4 COLLATE utf8mb4_0900_ai_ci;
 USE exempel;
```
{% endtab %}
{% endtabs %}

## Användare

Användartabellen kommer att kallas users, vi använder engelska för namnet och tabellen kommer att innehålla flera rader av typen _user_, därför blir det plural, _users_. Vilka kolumner behöver en tabell innehålla då?

* **id**, för att identifiera varje rad.
* **name**, användarnamnet för att identifiera användaren med. Maxlängd 32 tecken.
* **email**, för att kunna kontakta användaren på något sätt. En email kan teoretiskt sätt vara upp till 254 tecken lång, därför används datatypen varchar med en längd på 255 tecken.

Den första SQL-frågan skapar tabellen och lägger till id kolumnen.

{% tabs %}
{% tab title="SQL" %}
```sql
CREATE TABLE users (id INT UNSIGNED AUTO_INCREMENT, PRIMARY KEY(id)) 
ENGINE = innodb
DEFAULT CHARSET = utf8mb4;
```
{% endtab %}
{% endtabs %}

De andra kolumnerna läggs till efter att tabellen skapats. Ingen av kolumnerna får lämnas tomma och de ska vara unika för att undvika att flera personer använder samma användarnamn och eller email.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE users ADD name VARCHAR(32) NOT NULL UNIQUE;
ALTER TABLE users ADD email VARCHAR(255) NOT NULL UNIQUE;
```
{% endtab %}
{% endtabs %}

Den resulterande tabellen kan kontrolleras med DESCRIBE frågan.

{% tabs %}
{% tab title="SQL" %}
```sql
DESCRIBE users;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int unsigned | NO   | PRI | NULL    | auto_increment |
| name  | varchar(32)  | NO   | UNI | NULL    |                |
| email | varchar(255) | NO   | UNI | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
```
{% endtab %}
{% endtabs %}

Innan tabellens struktur är klar skapar du två kolumner med tidsstämplar för att underlätta administrationen av databasen. 

* **created\_at**, en timestamp för när användaren skapades.
* **updated\_at**, en timestamp för senaste uppdatering.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE users ADD created_at timestamp, ADD updated_at timestamp;
```
{% endtab %}
{% endtabs %}

Efter den sista ändringen är tabellen klar.

### Testdata

För att lägga till data i tabellen så använder vi INSERT. Värdet för id kolumnen ska aldrig sättas med insert, det sköts av databasens räknare\(auto\_increment\). Notera även att vi använder SQL-serverns inbyggda metod `now()` för att skapa tidsstämpeln för created\_at kolumnen.

{% tabs %}
{% tab title="SQL" %}
```sql
INSERT INTO users (name, email, created_at) VALUES ('jens', 'jens.andreasson@ga.ntig.se', now());
```
{% endtab %}
{% endtabs %}

Om frågan är ok så kan tabellens rader sedan väljas med SELECT frågan. \* väljer all data från den namngivna tabellen.

{% tabs %}
{% tab title="SQL" %}
```sql
SELECT * FROM users;
+----+------+----------------------------+---------------------+------------+
| id | name | email                      | created_at          | updated_at |
+----+------+----------------------------+---------------------+------------+
|  1 | jens | jens.andreasson@ga.ntig.se | 2020-09-25 12:37:51 | NULL       |
+----+------+----------------------------+---------------------+------------+
```
{% endtab %}
{% endtabs %}

## Meddelanden

Systemet vi skapar ska förutom användare ha en tabell för korta meddelanden, vi kallar de meeps. Precis som för users så är tabellnamnet i plural och tabellen skapas med en id kolumn.

{% tabs %}
{% tab title="SQL" %}
```sql
CREATE TABLE meeps (id INT UNSIGNED AUTO_INCREMENT, PRIMARY KEY(id)) 
ENGINE = innodb
DEFAULT CHARSET = utf8mb4;
```
{% endtab %}
{% endtabs %}

Efter att tabellen skapats så ändrar vi tabellen för att lägga till fler kolumner.

* **author**, användaren som skrivit meddelandet.
* **title**, meddelandets titel med en maxlängd på 128 tecken.
* **body**, själva meddelandet, ett text fält.
* **created\_at** och **updated\_at** kolumner med tidsstämplar.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE meeps ADD author VARCHAR (128) NOT NULL;
ALTER TABLE meeps ADD title VARCHAR (128) NOT NULL;
ALTER TABLE meeps ADD body TEXT NOT NULL;
ALTER TABLE meeps ADD created_at TIMESTAMP NULL, ADD updated_at TIMESTAMP NULL;
```
{% endtab %}
{% endtabs %}

Resultatet blir som följer efter en DESCRIBE fråga.

{% tabs %}
{% tab title="SQL" %}
```sql
describe meeps;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int unsigned | NO   | PRI | NULL    | auto_increment |
| author     | varchar(128) | NO   |     | NULL    |                |
| title      | varchar(128) | NO   |     | NULL    |                |
| body       | text         | NO   |     | NULL    |                |
| created_at | timestamp    | YES  |     | NULL    |                |
| updated_at | timestamp    | YES  |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
```
{% endtab %}
{% endtabs %}

### Testdata

Innan vi fortsätter att arbeta med tabellen behöver vi skapa testdata. Detta görs precis som tidigare med INSERT. Kör följande fråga några gånger, om du vill ändra så kan du ändra på innehållet, men behåll värdet i author kolumnen.

{% tabs %}
{% tab title="SQL" %}
```sql
INSERT INTO meeps (author, title, body, created_at)
VALUES ('Jens', 'Hello world', 'Lorem ipsum...', now());
```
{% endtab %}
{% endtabs %}

När du är klar så bör det finnas ett antal rader i tabellen med data.

{% tabs %}
{% tab title="SQL" %}
```sql
select * from meeps;
+----+--------+-----------------------------+--------------------------------------------+---------------------+------------+
| id | author | title                       | body                                       | created_at          | updated_at |
+----+--------+-----------------------------+--------------------------------------------+---------------------+------------+
|  1 | Jens   | Hello world                 | Lorem ipsum...                             | 2020-09-25 13:02:13 | NULL       |
|  2 | Jens   | Hello there                 | Lorem ipsum dolor sit...                   | 2020-09-25 13:04:34 | NULL       |
|  3 | Jens   | Hello                       | Lorem ipsum dolor sit amet...              | 2020-09-25 13:04:49 | NULL       |
|  4 | Jens   | Hello is anybody out there? | Lorem ipsum dolor sit amet, consectetur... | 2020-09-25 13:05:08 | NULL       |
+----+--------+-----------------------------+--------------------------------------------+---------------------+------------+
```
{% endtab %}
{% endtabs %}

### Author

Om du studerar tabellens data så ser du att författaren, author, kolumnen på varje inlägg hittills är densamma. Att upprepa data på det sättet, när det rör samma värde är onöskat och bryter mot [normaliseringen av en databas](https://docs.microsoft.com/sv-se/office/troubleshoot/access/database-normalization-description). Vad sker tillexempel om användaren Jens byter användarnamn till Snej, då måste alla fält uppdateras, det finns kanske inte heller en tydlig koppling till vem Jens är i systemet. Här kommer arbetet med relationerna i databasen in och hjälper oss att lösa detta.

Tidigare skapade vi tabellen users och skapa en användare i den. Vi kan nu använda detta för att ange författare i meeps tabellen. Första steget blir att ta bort author kolumnen.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE meeps DROP author;
```
{% endtab %}
{% endtabs %}

Efter att author kolumnen tagits bort så skapar vi en kolumn för användarens id med ett index \(för att snabba upp sökningar\). Kolumnen skapas först och sedan skapas ett index.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE meeps ADD user_id INT UNSIGNED;
CREATE INDEX user_id ON meeps (user_id);
```
{% endtab %}
{% endtabs %}

Efter denna uppdatering så har vi normaliserat tabellen, De existerande raderna behöver uppdateras för att koppla varje rad med rätt användar-id. Innan du uppdaterar raderna behöver du kontrollera vilket id som den användare du skapade har, detta så att följande fråga blir korrekt. Detta gäller även kolumnen meeps.id som måste anges och ändras vid frågorna.

{% tabs %}
{% tab title="SQL" %}
```sql
UPDATE meeps 
SET updated_at = now(), user_id = 1
WHERE meeps.id = 1;

UPDATE meeps 
SET updated_at = now(), user_id = 1
WHERE meeps.id = 2;

# och så vidare...
```
{% endtab %}

{% tab title="SQL" %}
```sql
select * from meeps;
+----+-----------------------------+--------------------------------------------+---------------------+---------------------+---------+
| id | title                       | body                                       | created_at          | updated_at          | user_id |
+----+-----------------------------+--------------------------------------------+---------------------+---------------------+---------+
|  1 | Hello world                 | Lorem ipsum...                             | 2020-09-25 13:02:13 | 2020-09-25 13:21:55 |       1 |
|  2 | Hello there                 | Lorem ipsum dolor sit...                   | 2020-09-25 13:04:34 | 2020-09-25 13:19:53 |       1 |
|  3 | Hello                       | Lorem ipsum dolor sit amet...              | 2020-09-25 13:04:49 | 2020-09-25 13:21:41 |       1 |
|  4 | Hello is anybody out there? | Lorem ipsum dolor sit amet, consectetur... | 2020-09-25 13:05:08 | 2020-09-25 13:21:42 |       1 |
+----+-----------------------------+--------------------------------------------+---------------------+---------------------+---------+
```
{% endtab %}
{% endtabs %}

## Join

För att koppla ihop tabellerna i en relationsdatabas så används JOIN i SELECT frågan. Det låter dig välja specifik data ur flera tabeller. Det är med hjälp av detta vi kan få tillgång till författarens namn för varje meddelande med hjälp av user\_id kolumnen.

{% tabs %}
{% tab title="SQL" %}
```sql
SELECT meeps.*, users.name FROM meeps JOIN users ON meeps.user_id = users.id;
+----+-----------------------------+--------------------------------------------+---------------------+---------------------+---------+------+
| id | title                       | body                                       | created_at          | updated_at          | user_id | name |
+----+-----------------------------+--------------------------------------------+---------------------+---------------------+---------+------+
|  1 | Hello world                 | Lorem ipsum...                             | 2020-09-25 13:02:13 | 2020-09-25 13:21:55 |       1 | jens |
|  2 | Hello there                 | Lorem ipsum dolor sit...                   | 2020-09-25 13:04:34 | 2020-09-25 13:19:53 |       1 | jens |
|  3 | Hello                       | Lorem ipsum dolor sit amet...              | 2020-09-25 13:04:49 | 2020-09-25 13:21:41 |       1 | jens |
|  4 | Hello is anybody out there? | Lorem ipsum dolor sit amet, consectetur... | 2020-09-25 13:05:08 | 2020-09-25 13:21:42 |       1 | jens |
+----+-----------------------------+--------------------------------------------+---------------------+---------------------+---------+------+
```
{% endtab %}
{% endtabs %}

Notera att vi specificerar vilka kolumner vi väljer från respektive tabell och syntaxen för detta, tabellnamn.column. \* är fortfarande en wildcard och betyder alla kolumner. 

## Mysql cmdline



