---
description: SQL är språket och MySQL en databashanterare
---

# SQL

## Introduktion

\*\*\*\*[**Structured Query Language \(SQL\)**](https://en.wikipedia.org/wiki/SQL) ****är ett språk för att arbeta med en [**relationsdatabas**](https://sv.wikipedia.org/wiki/Relationsdatabas). En relationsdatabas är organiserad i relationer. Relationer kallas även **tabeller** och dessa är organiserade i **rader** och **kolumner**.

För att arbeta med SQL krävs en **databashanterare**. Det finns ett stort antal databashanterare som använder SQL språket. Detta dokument behandlar databashanteraren [**MySQL** ](https://www.mysql.com/)och dess **klient** och **server**.

För att använda MySQL så behöver du köra dess klient och server. I avsnittet [MySQL](../utvecklarmiljo/wsl.md#mysql) finns instruktioner hur du installerar MySQL under Windows Subsystem for Linux \(WSL\).

### Starta server

Följande kommandon körs i **bash**.

{% tabs %}
{% tab title="Bash" %}
```bash
sudo service mysql restart
```
{% endtab %}
{% endtabs %}

Om du får ett \[ OK \] i terminalen har mysql **tjänsten** startats. Vill du kontrollera det ytterligare så kan du använda ett program som **htop** eller köra följande kommando, resultatet bör visa en eller flera **mysqld** processer.

{% tabs %}
{% tab title="Bash" %}
```bash
ps auxf | grep mysql
```
{% endtab %}
{% endtabs %}

### Starta klienten

När mysql-klienten startas behöver du ange en användare och ett lösenord. Kör följande för att koppla upp dig till din server på **localhost**.

{% tabs %}
{% tab title="Bash" %}
```bash
mysql -u username -p
```
{% endtab %}
{% endtabs %}

Om du lyckas koppla upp dig så bör du nu se mysql-prompten.

```sql
mysql>
```

{% hint style="info" %}
En databasserver kan användas av många olika klienter.
{% endhint %}

## Kommandon

### Skapa och använda en databas

{% hint style="info" %}
SQL-kommandon skrivs ofta i VERSALER, men språket i sig är inte **skifteskänsligt** \(engelska **case sensitive**\).
{% endhint %}

För att arbeta med en relationsdatabas behöver du först skapa eller välja en databas. Följande kommandon körs i mysql-prompten. Character set och det som följer sätter **teckenkodningen** till MySQLs implementering av **UTF-8**.

{% tabs %}
{% tab title="SQL" %}
```sql
 CREATE DATABASE databasnamn CHARACTER SET UTF8mb4 COLLATE utf8mb4_0900_ai_ci;
 USE databasnamn;
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
SQL-kommandon avslutas med ett **semikolon**, ;. Om du missar ett semikolon kommer prompten fortsätta kommandot på nya rader. Detta gör att du kan dela upp SQL kommandon på flera rader när du skriver dem. Skriv ett semikolon för att avsluta kommandot.
{% endhint %}

För att lista alla tillgängliga databaser på servern.

{% tabs %}
{% tab title="SQL" %}
```sql
SHOW databases;
```
{% endtab %}
{% endtabs %}

### Skapa en tabell i en databas

För att spara data behöver först en tabell skapas. När en tabell skapas krävs det att minst en kolumn skapas. Denna kolumn är som oftast en **primär** id-kolumn som databasens motor hanterar, detta är god [**databasdesign**](databasdesign.md).

{% hint style="warning" %}
Kontrollera att du har valt en databas.
{% endhint %}

CREATE TABLE skapar en tabell med angivet namn med en tillhörande kolumn. Hela kommandot skrivs här på tre rader.

{% tabs %}
{% tab title="SQL" %}
```sql
CREATE TABLE tabellnamn (id INT UNSIGNED AUTO_INCREMENT, PRIMARY KEY(id)) 
ENGINE = innodb
DEFAULT CHARSET = utf8mb4;
```
{% endtab %}
{% endtabs %}

Kolumnen som skapats har namnet id och är av typen **int** \(**heltal**, engelska **integer**\). Id kolumnen är även satt att vara **unsigned**, vilket gör att enbart icke negativa tal är tillåtna. **Auto increment** gör att databasen hanterar värdet på kolumnen, värdet uppdateras automatiskt med 1 för varje ny rad.

För att se en tabells innehåll används.

{% tabs %}
{% tab title="SQL" %}
```sql
DESCRIBE tabellnamn;

+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int unsigned | NO   | PRI | NULL    | auto_increment |
+-------+--------------+------+-----+---------+----------------+
1 row in set (0.00 sec)
```
{% endtab %}
{% endtabs %}

### Kolumner

För att skapa fler kolumner i en tabell så används **alter** tillsammans med **add**. Alter-kommandot kräver en tabell och ett kommando. Add-kommandot kräver ett namn, en datatyp och eventuellt andra parametrar.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE tabellnamn ADD kolumn datatyp parametrar;
```
{% endtab %}
{% endtabs %}

## Datatyper

Varje kolumn i en relationsdatabas har en datatyp som definierar vilken typ av värd som kan sparas. Några vanliga typer att börja med är:

* INT, heltal.
* DECIMAL\(p, s\), kräver att precisionen och storleken anges.
* VARCHAR\(l\), en sträng med variabel storlek, kräver att längden anges. 
* TEXT, för större strängar.
* TIMESTAMP, en datumstämpel.

Följande exempel skapar en textkolumn i databasen med datatypen varchar.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE users ADD name VARCHAR(30);
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
[På W3Schools finns en lista över datatyperna med beskrivningar.](https://www.w3schools.com/sql/sql_datatypes.asp)
{% endhint %}

## Parametrar

Utöver namnet på kolumnen samt datatypen kan en eller fler parametrar användas.

* NULL, kolumnens värde kan vara tomt.
* NOT NULL, kolumnens värde får inte vara tomt.
* UNSIGNED, ändrar en numerär kolumn så att den enbart kan innehålla icke negativa värden.
* AUTO\_INCREMENT, använder databasens räknare för värdet.
* UNIQUE, värdet måste vara unikt.

Följande exempel skapar en unik kolumn.

{% tabs %}
{% tab title="SQL" %}
```sql
ALTER TABLE users ADD email VARCHAR(255) UNIQUE;
```
{% endtab %}
{% endtabs %}

## Kommandon, fortsättning

* USE databasnamn, använd databas.
* SHOW databases eller tables, visa val.
* DESCRIBE tabell, beskriver eller visar en tabells struktur.
* DROP databas eller tabell, ta bort val.
* ALTER TABLE tabellnamn DROP kolumn, ta bort val.
* SELECT kolumner FROM tabellnamn, för att välja data.

## Kommandon, skapa data

För att skapa data i en kolumn används INSERT.

{% tabs %}
{% tab title="SQL" %}
```sql
INSERT INTO users (name, email) VALUES ("username","username@test.se");
```
{% endtab %}
{% endtabs %}

För att välja data används SELECT kommandot, det returnerar ett resultat. Select väljer kolumner från en tabell, det går antingen att specificera dessa eller använda ett **wildcard**, \*.

{% tabs %}
{% tab title="SQL" %}
```sql
SELECT name, email FROM users;
SELECT * FROM users;
```
{% endtab %}
{% endtabs %}

Utöver detta så kan den valda datan sorteras och begränsas. För att välja den senaste raden.

{% tabs %}
{% tab title="SQL" %}
```sql
SELECT kolumn FROM tabell ORDER BY kolumn DESC LIMIT 1;
```
{% endtab %}
{% endtabs %}

* ORDER BY kolumn, sorterar resultatet efter vald kolumn.
* DESC eller ASC väljer sorteringsordningen.
* LIMIT \#, begränsa resultatet till \# antal rader.

### Specifika rader

För att välja specifika rader i databasen så väljs dem utifrån värdet. Då används WHERE.

```sql
SELECT * FROM users WHERE name = 'username';
```

