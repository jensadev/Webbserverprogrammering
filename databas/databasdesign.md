# Databasdesign

För databasdesign brukar det pratas om normalisering, eller normalform. Det är ett antal regler som behöver efterföljas. Det är bra att känna till men här kommer det vara något förenklat.

När du ska designa tabeller så börja med att fundera på hur den data som ska sparas kommer att se ut. 

```text
# Blogg
författare, titel, text, datum
```

Utifrån detta så väljs sedan datatyper för de olika kolumnerna.

* Författare, text med ett namn, hur många tecken kan behövas, varchar\(200\)
* Titel, text med ett begränsat antal tecken, `varchar(150)`
* Text, detta kan vara en längre text, `text`
* Datum, `timestamp`

Den färdiga tabellen.

```sql
describe blog;
+------------+--------------+------+-----+---------+-------+
| Field      | Type         | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+-------+
| author     | varchar(200) | YES  |     | NULL    |       |
| titel      | varchar(150) | YES  |     | NULL    |       |
| body       | text         | YES  |     | NULL    |       |
| created_at | timestamp    | YES  |     | NULL    |       |
| updated_at | timestamp    | YES  |     | NULL    |       |
+------------+--------------+------+-----+---------+-------+
```

Med tabellen skapad så kan sedan data skapas i den.

```sql
INSERT INTO `blog`
    (
        `author`,
        `titel`,
        `body`,
        `created_at`,
        `updated_at`
    )
VALUES
    (
        'Jens',
        'Rubrik',
        'Exempel',
        now(),
        now()
    );
```

Kör frågan ovan ett antal gånger. Välj sedan all data med en select fråga.

```sql
select * from blog;
+--------+--------+---------+---------------------+---------------------+
| author | titel  | body    | created_at          | updated_at          |
+--------+--------+---------+---------------------+---------------------+
| Jens   | Rubrik | Exempel | 2020-11-25 13:17:39 | 2020-11-25 13:17:39 |
| Jens   | Rubrik | Exempel | 2020-11-25 13:17:41 | 2020-11-25 13:17:41 |
| Jens   | Rubrik | Exempel | 2020-11-25 13:17:41 | 2020-11-25 13:17:41 |
| Jens   | Rubrik | Exempel | 2020-11-25 13:17:42 | 2020-11-25 13:17:42 |
+------+--------+--------+---------+---------------------+---------------------+
4 rows in set (0.00 sec)
```

Detta fungerar okej än så länge och följer viss normalform\(vi har bara ett värde i varje fält, jämför med att vi hade sparat email i author tillsammans med namn\) men det visar också några problem.

* Data i author tabellen upprepas ofta, det är något som bryter mot normalformen.
* Det finns inget bra sätt att välja data. Om du vill hämta en rad ur denna databas, hur bestämmer du vilken?

För att normalisera tabellen behöver först varje rad ett ID så att de går att identifiera. Värdet i denna ID kolumn sköter databasen med auto\_increment. Det andra som ska göras är använda en relation för författarkolumnen.

```sql
describe blog;
+------------+-----------------+------+-----+---------+----------------+
| Field      | Type            | Null | Key | Default | Extra          |
+------------+-----------------+------+-----+---------+----------------+
| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| author_id  | bigint unsigned | YES  |     | NULL    |                |
| title      | varchar(200)    | YES  |     | NULL    |                |
| body       | text            | YES  |     | NULL    |                |
| created_at | timestamp       | YES  |     | NULL    |                |
| updated_at | timestamp       | YES  |     | NULL    |                |
+------------+-----------------+------+-----+---------+----------------+
```

Den data som tabellen innehåller ser nu ut som följer.

```sql
select * from blog;
+----+-----------+---------+-----------+---------------------+---------------------+
| id | author_id | title   | body      | created_at          | updated_at          |
+----+-----------+---------+-----------+---------------------+---------------------+
|  1 |         1 | Test    | Test text | 2020-11-25 13:31:23 | 2020-11-25 13:31:23 |
|  2 |         1 | Another | Text here | 2020-11-25 13:31:47 | 2020-11-25 13:31:47 |
+----+-----------+---------+-----------+---------------------+---------------------+
```

Det som saknas nu är en tabell för författaren. Likt föregående exempel så börjar det med en planering av vilken data som tabellen bör innehålla.

```text
# Användartabell
användarnamn, namn, email, timestamps
```

#### Uppgift

* Vilka datatyper passar och vilka kolumner behövs?

Tabellen kan sedan skapas. Notera att typen för id kolumen stämmer med author\_id ur blog tabellen.

```sql
describe users;
+------------+-----------------+------+-----+---------+----------------+
| Field      | Type            | Null | Key | Default | Extra          |
+------------+-----------------+------+-----+---------+----------------+
| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| handle     | varchar(64)     | YES  |     | NULL    |                |
| name       | varchar(200)    | YES  |     | NULL    |                |
| email      | varchar(255)    | YES  |     | NULL    |                |
| created_at | timestamp       | YES  |     | NULL    |                |
| updated_at | timestamp       | YES  |     | NULL    |                |
+------------+-----------------+------+-----+---------+----------------+
```

Med data.

```sql
select * from users;
+----+--------+-----------------+-------------------------+---------------------+---------------------+
| id | handle | name            | email                   | created_at          | updated_at          |
+----+--------+-----------------+-------------------------+---------------------+---------------------+
|  1 | Jens   | Jens Andreasson | jens.andreasson@ntig.se | 2020-11-25 13:37:39 | 2020-11-25 13:37:39 |
|  2 | Moa    | Moa Moasson     | moa@moa.se              | 2020-11-25 13:38:28 | 2020-11-25 13:38:28 |
+----+--------+-----------------+-------------------------+---------------------+---------------------+
```

Med data delat på flera tabeller så kommer relationsdatabasens styrkor fram. Genom att koppla author\_id till ett id ur users tabellen så går det nu att välja data från båda tabellerna med join.

```sql
select blog.title, blog.body, blog.created_at, users.name as author 
from blog 
left join users on blog.author_id = users.id;
+---------+-----------+---------------------+-----------------+
| title   | body      | created_at          | author          |
+---------+-----------+---------------------+-----------------+
| Test    | Test text | 2020-11-25 13:31:23 | Jens Andreasson |
| Another | Text here | 2020-11-25 13:31:47 | Jens Andreasson |
+---------+-----------+---------------------+-----------------+
```

Eftersom tabellerna nu har ID fält så går det även att välja specifika fält genom att identifiera dem med ID värdet.

```sql
select * from blog where id = 2;
+----+-----------+---------+-----------+---------------------+---------------------+
| id | author_id | title   | body      | created_at          | updated_at          |
+----+-----------+---------+-----------+---------------------+---------------------+
|  2 |         1 | Another | Text here | 2020-11-25 13:31:47 | 2020-11-25 13:31:47 |
+----+-----------+---------+-----------+---------------------+---------------------+
```

Samt att det enkelt går att välja poster skapade av en specifik författare.

```sql
select * from blog where author_id = 2;
```

