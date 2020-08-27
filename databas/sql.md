---
description: SQL är språket och MySQL en databashanterare
---

# SQL

## Introduktion

\*\*\*\*[**Structured Query Language \(SQL\)**](https://en.wikipedia.org/wiki/SQL) ****är ett språk för att arbeta med en [**relationsdatabas**](https://sv.wikipedia.org/wiki/Relationsdatabas). En relationsdatabas är organiserad i relationer. Relationer kallas även **tabeller** och dessa är organiserade i **rader** och **kolumner**.

För att arbeta med SQL krävs en **databashanterare**. Det finns ett stort antal databashanterare som använder SQL språket. Detta dokument behandlar databashanteraren [**MySQL** ](https://www.mysql.com/)och dess **klient** och **server**.

För att använda MySQL så behöver du köra dess klient och server. I avsnittet [MySQL](../utvecklarmiljo/wsl.md#mysql) finns instruktioner hur du installerar MySQL under Windows Subsystem for Linux \(WSL\).

### Starta server

Kommandot körs i WSL.

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

## Kommandon



