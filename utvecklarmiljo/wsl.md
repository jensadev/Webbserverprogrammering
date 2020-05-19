---
description: WSL
---

# Windows subsystem Linux

_“The Windows Subsystem for Linux lets developers run GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a virtual machine.”   -_    [Microsoft.com](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

I det här dokumentet finns ett stort antal instruktioner för hur du kommer igång och sätter upp en fungerande utvecklarmiljö som bygger på WSL med [Ubuntu](https://ubuntu.com/).

De flesta kommandon körs i terminalfönster, det är därför väldigt viktigt att du läser vad du ska göra, att du får kommandon att fungera innan du går vidare.

Jag kan inte skriva eller säga det nog många gånger och det är **-läs vad som sker på din skärm**.

Det är självklart bra om du har en grundläggande förståelse hur du navigerar dig runt i terminalen innan du gör detta. Behöver du fräscha upp minnet kring det, kolla [här](wsl.md#bash).

## Installation

För att kunna köra WSL så behöver du slå på denna feature i Windows. Det gör du genom att starta powershell som administratör.

```text
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

När du kört kommandot så kommer den att be dig att starta om datorn, gör det. När din dator startat om så startar du Microsoft Store och söker efter Ubuntu.

Välj Ubuntu LTS och installera. **Var noga med att skriva in ett användarnamn under installationen när den frågar.** Välj ett lösenord som du kommer ihåg!

När installationen är klar så startar du Ubuntu och kör följande kommandon.

```bash
sudo apt update
sudo apt upgrade
```

Ubuntu körs nu i ett fönster under windows och du har tillgång till det mesta du behöver utan att behöva virtualisera eller dual-boota. Om du behöver komma åt andra drivar eller Windows filer under Linux så hittar du dem under /mnt.

```bash
cd /mnt
ls -la
```

Detta ger dig en lista över de filsystem som är mountade under Linux.

## Visual Studio Code

Om du inte redan har Visual Studio Code installerat så gör detta nu. Du hittar det på  [https://code.visualstudio.com/](%20https://code.visualstudio.com/)

Code använder inte WSL som sitt standard shell utan det är något du behöver välja. För att byta shell i Code till WSL \(bash, Ubuntu\), så ändrar du i inställningarna.

```bash
inställningar-> sök efter terminal-> integrated-> shell: Windows, välj WSL
```

Nästa steg är att skapa en symlink för din code mapp så att du enkelt kan komma åt den. Navigera till din hem mapp i WSL och skapa länken. Detta förutsätter att du har en `c:\code` mapp. Om du inte har skapat en sådan tidigare så är det dags nu. Att samla sin kod på ett ställe är både praktiskt och bra praxis. 

```bash
cd
ln -s /mnt/c/code
ls -la
```

List kommandot bör visa dig att länken är skapad, testa sedan att köra.

```bash
cd code
ls -la
```

## LAMP server

LAMP är en förkortning för en webbserver med Linux, Apache, MySQL och PHP. Det finns flera varianter av detta då oftast första bokstaven byts ut beroende på vilket operativ system du använder, det vill säga WAMP, MAMP. Eftersom vi nu kör en Linux distribution under Windows så kommer vi att installera LAMP.

Enklaste sättet att göra detta på är att installera ett samlingspaket som finns i Ubuntu.

```bash
sudo apt install lamp-server^
```

Svara \[Y\] på frågorna och låt den ladda ner och installera paketen. Förhoppningsvis så går det problemfritt, om det strular så prova att köra om installationen.

När det är klart så behöver vi kolla om vi kan starta de två services vi behöver från LAMP, Apache och MySQL. Detta gör du genom att köra följande.

```bash
sudo service mysql restart
sudo service apache2 restart
```

När en service startar och fungerar så står det \[OK\] till höger i terminalen, eventuella meddelande är varningar eller annat, men så länge det står \[OK\] så är tjänsten startad. I annat fall står det \[FAIL\].

Om du fick \[OK\] så kan du nu öppna en webbläsare och surfa till [http://localhost](http://localhost).

Kolla vad felmeddelandet säger om det är problem. Med största sannolikhet så kommer du att få ett \[FAIL\] när du försöker starta Apache. Detta för att Windows ofta använder port 80, vilket är standardporten för en webbserver\(HTTP\). Vill du kolla vilka tjänster Windows kör så öppna en Powershell som administratör.

```bash
netstat -aon | findstr :80
```

Nu finns det två sätt att lösa detta problem. Antingen så får du avsluta och stänga av de tjänster som blockerar port 80, eller så du ändra vilken port webbservern ska använda. För att ändra porten så behöver vi redigera konfigurationsfilerna för detta.

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

Kör nu följande kommando för att skapa dig en användare. Byt ut `username` och `password` mot dina egna uppgifter. **Skriv ett lösenord som du kommer ihåg och inget “viktigt” lösenord.**

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

 sudo apt install phpmyadmin  


Under installationen välj

* apache2 server
* Database common
* config, generate pwd

Apache2 och php använder ett system för att slå på moduler och även sidor, så för att konfigurera och starta igång phmyadmin måste vi länka denna konfiguration. Börja med att gå till mappen 

 cd /etc/apache2/sites-available  


Skapa sedan en symlink till konfigurationsfilen

 sudo ln -s /etc/phpmyadmin/apache.conf phpmyadmin.conf  


När länken finns så kan vi “slå på” sidan

 sudo a2ensite phpmyadmin  


Du ska nu kunna surfa localhost/phpmyadmin från windows, fungerar det inte så kör

 sudo service apache2 restart

#### Userdir

För att kunna komma åt och spara dina filer så kan vi använda oss av en mod till apache som heter userdir.

För att starta denna behöver vi skriva

 sudo a2enmod userdir

 sudo service apache2 restart  


Du skapar sedan en mapp i ditt home som heter public\_html,

 cd

 mkdir public\_html  


Du ska nu kunna surfa till localhost/~DITTUSERNAME/  


För att kunna köra php från ditt userdir så behöver du dock göra några steg till.

Notera att det kan vara en annan version av php, då är det ett annat directory-namn.

 cd /etc/apache2/mods-available

 sudo nano php7.2.conf  


Kommentera sedan ut följande rader med \#

   &lt;IfModule mod\_userdir.c&gt;

        &lt;Directory /home/\*/public\_html&gt;

            php\_admin\_value engine Off

        &lt;/Directory&gt;

    &lt;/IfModule&gt;  


Starta sedan om apache2

 sudo service apache2 restart  


För att testa detta så skapar vi en fil i public\_html med namnet info.php, i den skriver du följande kod.

 &lt;?php phpinfo\(\); ?&gt;

## Node

För att installera och komma igång med webbservern Node så läs vidare här, [Node och Express](../node-och-express/)

## GitHub

Under WSL så är det väldigt enkelt att komma igång med Git också. Börja med att installera det.

```bash
sudo apt install git
```

Klart, du kan nu köra git från installationen. När du försöker commita för första gången så kommer du med största sannolikhet stöta på ett problem, vilket är att git vill veta vem det är som försöker commita. Du får då köra följande kommandon med dina uppgifter.

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

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
</table>Listan går att göra oändligt, läs mer [här](https://www.howtoforge.com/linux-commands/).

##     

