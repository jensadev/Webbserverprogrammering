---
description: WSL
---

# Windows subsystem Linux

_“The Windows Subsystem for Linux lets developers run GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a virtual machine.”   -_    [Microsoft.com](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

I det här dokumentet finns ett stort antal instruktioner för hur du kommer igång och sätter upp en fungerande utvecklarmiljö som bygger på WSL med [Ubuntu](https://ubuntu.com/).

De flesta kommandon körs i terminalfönster, det är därför väldigt viktigt att du läser vad du ska göra, att du får kommandon att fungera innan du går vidare.

Jag kan inte skriva eller säga det nog många gånger och det är **-läs vad som sker på din skärm**.

Det är självklart bra om du har en grundläggande förståelse hur du navigerar dig runt i terminalen innan du gör detta. ls -la för att kolla vad som finns i en mapp, cd för att byta osv. och kom ihåg att tabba för att underlätta för dig så att du skriver rätt.

## Installation

Du behöver slå på denna feature först. I powershell med adm. rättigheter så skriver du.

 Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux  


Boota om när den ber dig göra det.

Starta microsoft-store och sök efter Ubuntu.

Hämta och installera. Var noga med att skriva in ett användarnamn när den frågar.

Välj ett lösenord som du kommer ihåg.  


I ubuntu kör

 sudo apt update

 sudo apt upgrade  


Ubuntu körs nu i ett fönster under windows och du har tillgång till det mesta du behöver utan att behöva virtualisera eller dualboota. Om du behöver komma åt andra drivar eller windows filer under linux så hitta du dem under /mnt katalogen.

 /mnt/c osv.

### Visual studio code

Installera vscode om du inte redan har det \(på windows alltså!\). [https://code.visualstudio.com/](https://code.visualstudio.com/)  


För att byta shell i VSCode till WSL \(bash från ubuntu\), 

inställningar&gt;sök efter terminal&gt;hitta terminal&gt; integrated&gt; shell: Windows, välj WSL  


Nu behöver du skapa en symlink till din code mapp på c:, detta så att vi kan arbeta i den och VSCode kan hitta filerna.

För att göra detta skriver du \(i din hem-mapp, $\),

 cd

 ln -s /mnt/c/code code

### LAMP

Avinstallera XAMPP \(om du har det\), exportera dina databaser om du behöver spara något och kolla om du har något arbete i htdocs.  


I en admin powershell, kör netstat -aon \| findstr :80 för att se om något använder port 80. Port 80 är default för webbserver \(utan https\), vi behöver den. Det kan vara en myriad av ondskefulla tjänster som tar porten.  


I ubuntu,

 sudo apt install lamp-server^  


Detta installerar paketet lamp-server, med mysql, php och apache2. Om så önskas kan mysql sedan ersättas med mariadb.  


Testa att starta upp mysql och apache2 tjänster.

 sudo service mysql start

 sudo service apache2 start  


När en service startar och fungerar så står det \[OK\] till höger i terminalen, ev meddelande är varningar eller annat, men så länge det står \[OK\] så är tjänsten startad.

Om apache strejkar så står det \[FAIL\] på att starta tjänsten, det beror oftast på att port 80 är upptagen. För att ändra vilken port apache använder så får du redigera en fil.

  cd /etc/apache2

  sudo nano ports.cfg  


Ändra raden där det står Listen 80 till Listen 88. Spara och starta om servicen igen.

Du kommer hädanefter att behöva ange vilken port som ska användas när du surfar till din localhost, det gör du med ett kolon.  


  [http://localhost:88](http://localhost:88)

  [http://localhost:88/~username](http://localhost:88/~username)  


Om det funkar ska du kunna surfa localhost från windows.  


Har du startat mysql och tjänsten är igång så kan du nu använda mysql klienten för att koppla upp dig till din server, skriv

 sudo mysql -u root  


Vi använder här sudo\(superuser\) för att komma åt servern från klienten och vi använder användaren root, det är inget vi ska fortsätta göra, så vi behöver skapa en användare till oss.

Så när du kommer in i mysql kör följande kommando för att skapa dig en användare. Byt ut username och password mot dina uppgifter. Skriv ett lösenord som du kommer ihåg och inget “viktigt” lösenord.

 GRANT ALL PRIVILEGES ON \*.\* TO 'username'@'localhost' IDENTIFIED BY 'password';  


Skriv exit för att komma ur.

Du har nu skapat en user och databas-servern bör fungera. Testa det genom att köra

 mysql -u USERNAME -p   


 Kommer du in? Bra! Kör sedan

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

#### Laravel

Notera att även om du inte ska installera Laravel, utan att du ska köra tillexempel phpunit för tester, så krävs ett antal av extra-paketen som listas här, så följ instruktionerna.

Composer är en pakethanterare för php, \([https://getcomposer.org](https://getcomposer.org)\).

 sudo apt install composer  


För att installera laravel så krävs det ett antal paket igång i php, du kan läsa mer om det här [https://laravel.com/docs/5.7/installation](https://laravel.com/docs/5.7/installation)

För att installera modulerna så kör du, listan på paket står lite längre ned,

 sudo phpenmod PAKETNAMN  


Vi behöver ladda ned och installera paket genom apt också, 

 sudo apt install php-bcmath  


Efter det kan vi slå på varje modul, du kan behöva installera dem med apt

pdo, mbstring, tokenizer, xml, ctype, json, bcmath  


Sedan kör du 

 composer global require laravel/installer

  
Detta kommer att ladda ned ett antal filer och installera laravel.

Testa om du kan köra laravel, annars behöver vi lägga till det i din PATH.

I din hem-mapp så behöver du redigera din .profile, använd cd för att komma dit

 nano .profile  


Längst ned i filen så klistrar du in

\# PATH

export PATH="$PATH:$HOME/.config/composer/vendor/bin"  


Detta kommer då lägga till composer mappen i din PATH utöver systemets PATH\(som finns i /etc/environment\)

Logga ut och in och du ska nu kunna köra laravel kommandot.  


Cda sedan in i code mappen, cd code och fortsätt med att skapa laravel projektet.

För att skapa ett projekt kör du

 laravel new PROJEKTNAMN  


Det tar en stund då den laddar ned ett stort antal paket.

 cd projektnamn

 php artisan serve  


Funkar det inte, php artisan key:generate kan hjälpa.

Du ska nu kunna skriva code . i projektets mapp och starta Visual studio code från foldern.

### NodeJS

 sudo apt install nodejs

 sudo apt install npm  


Efter det så kan du installera några av de paket vi använt globalt, dvs. med -g flaggan, det kräver sudo.

pug-cli, express-generator, sass

## Resultat

Du har nu skapat en grym utvecklingsmiljö där du kan skapa både node appar men även köra databas, lamp osv. Vi slipper XAMPP som är tveksamt och kan köra det under linux istället\(där vi har mycket bättre möjligheter att uppdatera osv.\)

Miljön använder linux/bash vilket är standarden inom det här området.

Visual studio code låter dig utveckla projekt i alla möjliga språk.  
  
  
  


