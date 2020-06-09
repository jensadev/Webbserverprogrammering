---
description: WSL
---

# Windows Subsystem for Linux

> _“The Windows Subsystem for Linux lets developers run GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a virtual machine.”   -_    [Microsoft.com](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

Det här dokumentet innehåller ett flertal instruktioner för hur du kommer igång och sätter upp en fungerande utvecklarmiljö med **Windows Subsystem for Linux \(WSL\)** och [**Ubuntu**](https://ubuntu.com/).

Majoriteten av de kommandon som visas kör du i **terminalen**, genom ett **shell**. Det är därför väldigt viktigt att du följer instruktionerna noggrant och att du får kommandona att fungera innan du går vidare. Jag kan inte skriva eller säga det nog många gånger och det är:

{% hint style="warning" %}
Läs vad som sker på din skärm! Nästan alla kommandon ger felmeddelanden när det inte fungerar. Många kommandon ger inte något meddelande alls när de fungerade.
{% endhint %}

För att få ut det mesta ur detta dokument och förstå så är det självklart bra om du har en grundläggande kunskap om hur du använder terminalen; **Bash** eller **Powershell**. I slutet av dokumentet finns det en samlad lista över användbara [kommandon](wsl.md#bash).

## Installation

Windows kräver att du slår på en feature för att kunna installera WSL. Starta Powershell som administratör och kör följande kommando.

```text
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

Windows kommer att be dig starta om datorn när kommandot är utfört. När din dator startat om så startar du sedan **Microsoft Store** och söker efter **Ubuntu**.

Välj senaste versionen av Ubuntu LTS och installera. 

{% hint style="danger" %}
Skriv in ett användarnamn under installationen när den frågar.   
****Välj ett lösenord som du kommer ihåg!
{% endhint %}

När installationen är färdig så startar du Ubuntu och kör följande kommandon med **apt**, apt är Ubuntus pakethanterare. Först uppdaterar apt Ubuntus listor över paket. Sedan uppgraderar apt de paket som behöver uppdateras.

```bash
sudo apt update
sudo apt upgrade
```

{% hint style="info" %}
Kommandot **sudo** låter dig köra program med rättigheterna för superuser, Linux root konto. Det behövs ofta när du ska installera program. Försök undvika det om möjligt av säkerhetsskäl.
{% endhint %}

Ubuntu körs nu i ett fönster under Windows och ger dig tillgång till ett flertal program som används vid webbutveckling. Det utan att behöva [virtualisera](https://en.wikipedia.org/wiki/Virtualization) eller [dual-boota](https://en.wikipedia.org/wiki/Multi-booting) Linux. 

För att komma åt andra diskar eller partitioner med Windows filer på när du kör Ubuntu så hittar du dem i /mnt.

```bash
cd /mnt
ls -la
```

Detta ger dig en lista över de filsystem ****som är mountade ****under Linux. Kör du en skoldator med en disk så kommer du med största sannolikhet se`c`, vilket är disken som är`c:` under Windows.

## Visual Studio Code

Innan du fortsätter behöver du installera **Visual Studio Code** \(förkortat till **vscode**\). Du kan hämta det [här](https://code.visualstudio.com/). 

I vscode är inte WSL dess standard shell. För att byta shell i vscode behöver du ändra inställningarna.

```bash
I menyn -> Terminal -> New Terminal
```

Vscode kommer att öppna ett nytt terminalfönster, för att välja default shell klickar du som på bilden och väljer sedan WSL.

![V&#xE4;lj default shell.](../.gitbook/assets/defshell.png)

Nästa steg är att skapa goda förutsättningar för att arbeta med kod i vscode. Börja med att skapa en **mapp** i `c:` med namnet `code`. Skapandet av mappen gör du i Windows. 

{% hint style="info" %}
Att samla sin kod på ett ställe är både praktiskt och bra praxis.
{% endhint %}

För att enkelt komma åt code-mappen skapar du en [**symbolisk länk**](https://sv.wikipedia.org/wiki/Symbolisk_l%C3%A4nk) **\(symlink\)** till den. Navigera till din **hem-mapp** i WSL och skapa länken med följande kommandon. 

```bash
cd
ln -s /mnt/c/code
ls -la
```

List kommandot bör visa dig att länken är skapad.

```bash
lrwxrwxrwx 1 jens jens       11 Feb 17  2019 code -> /mnt/c/code
```

Navigera sedan in i code-mappen och lista innehållet.

```bash
cd code
ls -la
```

## GitHub

Installera sedan **Git** i WSL. 

```bash
sudo apt install git
```

När det är klart kan du köra Git. Vid din första **commit**, så kommer Git att efterfråga dina användaruppgifter. ****Ange dem med följande kommandon.

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

De kommandon du behöver känna till för att köra Git från terminalen finns i detta [Cheat Sheet](https://github.github.com/training-kit/downloads/github-git-cheat-sheet.pdf).

## LAMP server

I webbserver-kursen kommer du att få prova på ett antal olika typer av **webbservrar**. [**LAMP** ](https://en.wikipedia.org/wiki/LAMP_%28software_bundle%29)är en akronym för en typ av server och betyder en webbserver med [Linux](https://www.linux.org/), [Apache](https://www.apache.org/), [MySQL](https://www.mysql.com/) och [PHP](https://www.php.net/). Det finns flera varianter av **\*AMP** där första bokstaven byts ut beroende på vilket operativsystem du använder, till exempel **WAMP** och **MAMP**. Eftersom du kör en Linux distribution, Ubuntu, under Windows så installerar du en LAMP-server.

Ubuntu har en färdig samling paket för LAMP-server.

```bash
sudo apt install lamp-server^
```

Svara \[Y\] på frågorna och vänta på att den ska ladda ner och installera paketen. Förhoppningsvis så går det bra, strular det så prova att starta om installationen.

Apache och MySQL är de två **services** som behöver startas efter att installationen är slutförd.

```bash
sudo service mysql restart
sudo service apache2 restart
```

{% hint style="info" %}
När en service startar och fungerar så står det \[OK\] till höger i terminalen. Eventuella meddelande är varningar eller annat. Så länge det står \[OK\] så är tjänsten startad.  
Annars står det \[FAIL\].
{% endhint %}

Fungerade det att starta din service, \[OK\], så kan du nu öppna en webbläsare och surfa till [http://localhost](http://localhost).

{% hint style="info" %}
\*\*\*\*[**localhost**](https://en.wikipedia.org/wiki/Localhost) ****är ett alias för din dators loopback interface som har ip-adressen 127.0.0.1
{% endhint %}

Om det står ett felmeddelande, \[FAIL\], så behöver det felsökas. Det vanligaste felet när du försöker starta Apache är att **port 80** är blockerad. Detta för att Windows ofta använder port 80, 80 är standardporten för en webbserver\(HTTP\). Du kan undersöka vilka tjänster Windows genom Powershell.

```bash
netstat -aon | findstr :80
```

Det två sätt att åtgärda problemet med att Windows använder port 80. Antingen avslutar du och stänger av tjänsterna som blockerar port 80, eller så ändrar du vilken port Apache ska använda. För att konfigurera om vilken port Apache använder så utför du följande.

```bash
cd /etc/apache2
sudo nano ports.cfg
```

Ändra raden där det står `Listen 80` till `Listen 88`. För att spara i nano trycker du `ctrl+o`, följt av enter, sedan `ctrl+x` för att avsluta.

Testa sedan att starta om Apache igen. Om du får \[OK\] så kan du nu öppna en webbläsare och surfa till [http://localhost:88](http://localhost:88). Notera att du nu måste ange `:88` i slutet av adressen för att det ska fungera.

### MySQL

Har du startat MySQL och tjänsten är igång så kan du nu använda MySQL klienten för att koppla upp dig. Det är nämligen så att det du installerat och startat är en server-tjänst för databasen MySQL. Denna server ligger nu i bakgrunden och väntar på att användas. Den använder port 3306 som standard och det är inte något vi behöver ändra. 

För att koppa upp oss mot server kan vi använda olika former av klienter, men för att göra det så behöver vi en användare och ett lösenord. Så första gången vi gör detta så kommer vi att använda root användaren, notera att vi gör bara detta denna gång för att skapa en annan användare. Detta är av säkerhetsskäl.

```bash
sudo mysql -u root
```

Vi använder här sudo\(super user\) för att komma åt servern från klienten med användaren root\(-u root\). Om det fungerar som det ska så möts du nu av en prompt `>`.

Kör nu följande kommando för att skapa dig en användare. Byt ut `username` och `password` mot dina egna uppgifter.

{% hint style="warning" %}
Skriv ett lösenord som du kommer ihåg och inget “viktigt” lösenord.
{% endhint %}

```sql
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' IDENTIFIED BY 'password';
```

Kommandot skapa en användare för alla databaser på localhost med username och password. Kör följande för att avsluta. 

```bash
exit
```

Du är nu redo att testa din användare med hjälp av MySQL klienten.

```bash
mysql -u username -p
```

 Kommer du in? **Bra!**

### phpMyAdmin

Vi kommer att arbeta med SQL direkt från MySQL klienten, då det är viktigt att förstå sig på de SQL kommandon som körs. Men för att förenkla delar av arbetet med MySQL så kommer vi att installera ett grafiskt administrationsverktyg som heter [phpMyAdmin](https://www.phpmyadmin.net/). 

```bash
sudo apt install phpmyadmin
```

Under installationen så väljer du

* [ ] apache2 server
* [ ] Database common
* [ ] config, generate pwd

Apache2 och PHP använder ett system för att slå på moduler och sidor, så för att konfigurera och starta igång phpMyAdmin måste vi länka denna konfiguration. För att göra det så behöver vi gå till rätt mapp och sedan skapa en symlink till konfigurationsfilen.

```bash
cd /etc/apache2/sites-available
sudo ln -s /etc/phpmyadmin/apache.conf phpmyadmin.conf
ls -la
```

Nu bör du se en länk som heter phpmyadmin.conf och som pekar till källan.

```bash
lrwxrwxrwx 1 root root   27 Feb 17  2019 phpmyadmin.conf -> /etc/phpmyadmin/apache.conf
```

När länken väl finns så kan vi "slå på" sidan i Apaches konfiguration. Detta görs med `a2ensite` kommandot vilket behöver köras som sudo.

```bash
sudo a2ensite phpmyadmin
```

Starta sedan om Apache och surfa sedan till [http://localhost/phpmyadmin](http://localhost/phpmyadmin) eller [http://localhost:88/phpmyadmin](http://localhost:88/phpmyadmin) om du behövde ändra vilken porten som Apache använder.

### Userdir

För att förenkla hur du jobbar med webbservern så ska vi använda oss av en modul till apache som heter userdir. Userdir låter oss skapa en mapp som är kopplad till webbservern i vår egen hem mapp i Linux.

För att slå på denna mod så kör och starta sedan om Apache.

```bash
sudo a2enmod userdir
sudo service apache2 restart
```

Du behöver sedan skapa en mapp i din home mapp som heter public\_html.

```bash
cd
mkdir public_html
```

Du ska nu kunna surfa till http://localhost/~USER eller http://localhost:88/~USER, där USER är ditt username.

### PHP

Hittills har vi inte behövt använda PHP till något, men för att kunna använda det från public\_html behöver vi ändra på lite konfiguration.

```bash
cd /etc/apache2/mods-available
ls -la php*
sudo nano phpX.X.conf # X är versionsnumret
```

Notera att det kan vara en annan version av PHP, därför kollar du vilken version du behöver skriva. I filen så kommenterar du sedan ut följande rader med `#`

```bash
<IfModule mod_userdir.c>
    <Directory /home/*/public_html>
        php_admin_value engine Off
    </Directory>
</IfModule>
```

 Starta sedan om Apache, så är vi nästan klar. För att se om PHP är igång från home mappen så gör följande.

```bash
cd
cd public_html
echo "<?php phpinfo(); ?>" > info.php
```

Surfa sedan till localhost/~USER och öppna `info.php`, om det fungerar som det ska så bör du få information kring din webbserver och dess konfiguration. Funkar inte PHP, ja då skriver den ut koden du just skrev eftersom Apache inte förstår att den ska tolkas som PHP.

```php
<?php phpinfo(); ?>
```

## Node.js

Apache är en webbserver och Node är en annan. Vi kommer att arbeta mer med Node i nästa avsnitt,  [Node och Express](../node/node-och-express/)

## Resultat

Du har nu installerat och skapat en grym utvecklingsmiljö där du kan skapa både Node appar men även köra databas, LAMP och så vidare. Vi slipper krångliga Windows lösningar som XAMPP och vi kan köra programmen under WSL Linux vilket ger oss mycket större kontroll.

Miljön använder Linux/bash vilket är standarden inom det här området och otroligt viktigt att kunna.

Visual Studio Code låter dig utveckla projekt i en myriad av olika språk med alla dess paket.

## Bash

Att kunna navigera i bash är en förutsättning för användningen av WSL. Här är några av de kommandon som du bör lära dig. De flesta kommandon i linux ger dig bra hjälp om du skriver.

```bash
kommando --help
```

<table>
  <thead>
    <tr>
      <th style="text-align:left">Kommando</th>
      <th style="text-align:left">F&#xF6;rklaring</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">sudo</td>
      <td style="text-align:left">sudo kommando, anv&#xE4;nds n&#xE4;r du beh&#xF6;ver k&#xF6;ra n&#xE5;got
        som superuser</td>
    </tr>
    <tr>
      <td style="text-align:left">ls -la</td>
      <td style="text-align:left">list, visa alla filer och mappar i nuvarande mapp</td>
    </tr>
    <tr>
      <td style="text-align:left">cd</td>
      <td style="text-align:left">
        <p>change directory, byt mapp till den du skriver efter kommandot.</p>
        <p>Skriver du enbart cd kommer du till din hem mapp /home/username</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">mkdir</td>
      <td style="text-align:left">mkdir namn, skapa en ny mapp med namn</td>
    </tr>
    <tr>
      <td style="text-align:left">rm -rf</td>
      <td style="text-align:left">remove, -rf f&#xF6;r att ta bort mappar, f&#xF6;ljt av filnamn</td>
    </tr>
    <tr>
      <td style="text-align:left">cp</td>
      <td style="text-align:left">copy, kopiera fil A till B</td>
    </tr>
    <tr>
      <td style="text-align:left">mv</td>
      <td style="text-align:left">move, flytta fil A till B</td>
    </tr>
    <tr>
      <td style="text-align:left">nano</td>
      <td style="text-align:left">nano &#xE4;r ett textredigeringsprogram</td>
    </tr>
    <tr>
      <td style="text-align:left">cat</td>
      <td style="text-align:left">cat filnamn, f&#xF6;r att skriva ut en fil i terminalen</td>
    </tr>
    <tr>
      <td style="text-align:left">history</td>
      <td style="text-align:left">
        <p>history ger dig en lista &#xF6;ver vilka tidigare kommadon du k&#xF6;rt.</p>
        <p>Du kan upprepa kommandon fr&#xE5;n historia med !##, d&#xE4;r ## &#xE4;r
          siffran</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">apt</td>
      <td style="text-align:left">
        <p>apt &#xE4;r Ubuntus pakethanterare som du anv&#xE4;nder f&#xF6;r att installera
          program.</p>
        <p>apt update, apt upgrade, apt install</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">man</td>
      <td style="text-align:left">man kommando, ger dig manualen f&#xF6;r det kommando du namnger</td>
    </tr>
    <tr>
      <td style="text-align:left">ln -s</td>
      <td style="text-align:left">link -s f&#xF6;r att skapa en symbolisk l&#xE4;nk, fr&#xE5;n fil A till
        B</td>
    </tr>
  </tbody>
</table>

Listan går att göra oändlig, läs mer [här](https://www.howtoforge.com/linux-commands/).

##     

