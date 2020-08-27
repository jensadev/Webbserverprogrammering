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

```bash
sudo service mysql restart
```

Om du får ett \[ OK \] i terminalen har mysql **tjänsten** startats. Vill du kontrollera det ytterligare så kan du använda ett program som **htop** eller köra följande kommando, resultatet bör visa en eller flera **mysqld** processer.

```bash
ps auxf | grep mysql
```

### Starta klienten

När mysql-klienten startas behöver du ange en användare och ett lösenord. Kör följande för att koppla upp dig till din server på **localhost**.

```bash
mysql -u username -p
```

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

```sql
 CREATE DATABASE databasnamn CHARACTER SET UTF8mb4 COLLATE utf8mb4_0900_ai_ci;
 USE databasnamn;
```

{% hint style="info" %}
SQL-kommandon avslutas med ett **semikolon**, ;. Om du missar ett semikolon kommer prompten fortsätta kommandot på nya rader. Detta gör att du kan dela upp SQL kommandon på flera rader när du skriver dem. Skriv ett semikolon för att avsluta kommandot.
{% endhint %}

För att lista alla tillgängliga databaser på servern.

```sql
SHOW databases;
```

### Skapa en tabell i en databas

För att spara data behöver först en tabell skapas. När en tabell skapas krävs det att minst en kolumn skapas. Denna kolumn är som oftast en **primär** id-kolumn som databasens motor hanterar, detta är god [**databasdesign**](databasdesign.md).

{% hint style="warning" %}
Kontrollera att du har valt en databas.
{% endhint %}

Create table skapar en tabell med angivet namn med en tillhörande kolumn. Hela kommandot skrivs här på tre rader.

```sql
CREATE TABLE tabellnamn (id INT UNSIGNED AUTO_INCREMENT, PRIMARY KEY(id)) 
ENGINE = innodb
DEFAULT CHARSET = utf8mb4;
```

Kolumnen som skapats har namnet id och är av typen **int** \(**heltal**, engelska **integer**\). Id kolumnen är även satt att vara **unsigned**, vilket gör att enbart icke negativa tal är tillåtna. **Auto increment** gör att databasen hanterar värdet på kolumnen, värdet uppdateras automatiskt med 1 för varje ny rad.

För att se en tabells innehåll används.

```sql
DESCRIBE tabellnamn;

+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int unsigned | NO   | PRI | NULL    | auto_increment |
+-------+--------------+------+-----+---------+----------------+
1 row in set (0.00 sec)
```

### Kolumner

För att skapa fler kolumner i en tabell så används **alter** tillsammans med **add**. Alter-kommandot kräver en tabell och ett kommando. Add-kommandot kräver ett namn, en datatyp och eventuellt andra parametrar.

```sql
ALTER TABLE tabellnamn ADD kolumn datatyp parametrar;
```

## Datatyper

Varje kolumn i en relationsdatabas har en datatyp som definierar vilka värden som kan sparas i kolumnen. Några vanliga typer att börja med är:

* int, heltal.
* decimal\(p, s\), kräver att precisionen och storleken anges.
* varchar\(l\), en sträng med variabel storlek, kräver att längden anges. 
* text, för större strängar.
* timestamp, en datumstämpel.

{% hint style="info" %}
[På W3Schools finns en lista över datatyperna med beskrivningar.](https://www.w3schools.com/sql/sql_datatypes.asp)
{% endhint %}

## Parametrar

Det

* null, kolumnens värde kan vara tomt
* not null, kolumnens värde får inte vara tomt
* unsigned
* auto\_increment
* unique

## Kommandon, fortsättning

## Kommandon, skapa data

