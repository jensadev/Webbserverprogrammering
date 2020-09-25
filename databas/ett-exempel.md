# Ett exempel

För att omsätta teorin från [SQL ](sql.md)avsnittet kommer vi nu att skapa en två tabeller och välja data ur dem. Exemplet kommer vara en tabell för användare och en för kortare meddelanden. Tabellerna kommer att länkas med användarens id.

Det första som behöver skapas är en databas.

{% tabs %}
{% tab title="SQL" %}
```sql
 CREATE DATABASE exempel CHARACTER SET UTF8mb4 COLLATE utf8mb4_0900_ai_ci;
 USE exempel;
```
{% endtab %}
{% endtabs %}

## Users

Användartabellen kommer att kallas users, vi använder engelska för namnet och tabellen kommer att innehålla flera rader av typen _user_, därför blir det plural, _users_. Vilka kolumner behöver en tabell innehålla då?

* id, för att identifiera varje rad.
* name, användarnamnet för att identifiera användaren med. Maxlängd 32 tecken.
* email, för att kunna kontakta användaren på något sätt. En email kan teoretiskt sätt vara upp till 254 tecken lång, därför används datatypen varchar med en längd på 255 tecken.

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

* created\_at, en timestamp för när användaren skapades.
* updated\_at, en timestamp för senaste uppdatering.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE users ADD created_at timestamp, ADD updated_at timestamp;
```
{% endtab %}
{% endtabs %}

Efter den sista ändringen är tabellen klar.

### Testdata

För att lägga till data i tabellen så använder vi INSERT. Notera att vi använder SQL-serverns inbyggda metod `now()` för att skapa tidsstämpeln för created\_at kolumnen.

{% tabs %}
{% tab title="SQL" %}
```sql
INSERT INTO users (name, email, created_at) VALUES ('jens', 'jens.andreasson@ga.ntig.se', now());
```
{% endtab %}
{% endtabs %}

Om queryn är ok så kan datan sedan väljas med SELECT frågan. \* väljer all data från den namngivna tabellen.

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

