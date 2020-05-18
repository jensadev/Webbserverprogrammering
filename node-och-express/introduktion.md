# Introduktion

## Installation och förberedelse

Du behöver köra Ubuntu under[ Windows subsystem for Linux\(WSL\)](https://docs.microsoft.com/en-us/windows/wsl/install-win10). Vi kommer att köra [Node ](https://nodejs.org/)version 12, vilket är deras long term support\(LTS\) version\(när det här skrivs\). För att installera detta behöver vi göra lite annorlunda än vanligt med [Ubuntu](https://ubuntu.com/), detta för att standardversion för Ubuntu är mycket äldre.

```text
sudo apt update
sudo apt upgrade

sudo apt install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

sudo apt install nodejs
```

Nu kan du kolla din version av Node med\(bör vara 12.x\)

```text
node --version
```

Du bör även ha [npm](https://www.npmjs.com/)\(node package manager\) installerat nu, kör\(bör vara 6.x\).

```text
npm --version
```

Nu när vi har Node installerat tillsammans med npm så kan vi installera Express. [Express](https://expressjs.com/) är ett minimalistiskt ramverk för Node. Express låter oss skapa routes och annat för vår applikation. Vi kommer även att installera en generator för Express för att förenkla vår setup\([scaffolding](https://en.wikipedia.org/wiki/Scaffold_%28programming%29)\). Vi kommer att installera Express globalt på systemet, därför kommer du att behöva köra följande som sudo.

```text
sudo npm i -g express express-generator
```

För att kolla om det gick som tänkt så kan du köra.

```text
express --help
```

### Ett första projekt

För att skapa grunden för ett projekt så kör du enklast express i den mapp du arbetar i\(var noga\). Skapa en mapp för ditt projekt, gå in i den\(cd\) och kör Express.

```text
mkdir PROJEKTNAMN
cd PROJEKTNAMN
express --view=pug --css sass --git
```

Detta ger oss grunden för en vår app. Vi kör kommandot med tilläggen view, css och git. Detta låter oss välja vilken view motor vi vill att express ska använda, vilket är [Pug](https://pugjs.org/). Vi säger även till Express att vi ska använda Sass för css. Slutligen så skapar express en .gitignore fil åt oss så att vi inte laddar upp saker som inte har något på git att göra\(som node\_modules mappen\).

För att färdigställa installationen så behöver du installera de paket som Express använder. Paketen är listade i filen package.json som npm använder för att hålla koll på vilka paket som din app använder. Paketen är följande.

{% code title="package.json" %}
```javascript
"dependencies": {
  "cookie-parser": "~1.4.4",
  "debug": "~2.6.9",
  "express": "~4.16.1",
  "http-errors": "~1.6.3",
  "morgan": "~1.9.1",
  "node-sass-middleware": "0.11.0",
  "pug": "2.0.0-beta11"
},
```
{% endcode %}

För att installera paketen kör du.

```text
npm install
```

Notera att när du kör npm install så skapas också en package-lock.json, vilket innehåller information om alla paketen du installerat. Om kommandot kördes utan några fel \(vid fel står det npm ERR!\) så kan du nu starta din server.

```text
npm start
```

Surfa sedan till [localhost:3000](http://localhost:3000/) och du är igång! Denna server ligger uppe så du kan även komma åt den från andra devices om du har din ip-adress.

### Nodemon

Ett väldigt praktiskt tillägg till Node är nodemon, det låter oss starta en server som automatiskt startar om när vi ändrar på filer. Detta gör att vi slipper sitta och starta om Node när vi ändrar i någon av projektets filer.

```text
npm install --save-dev nodemon
```

Sedan redigerar vi package.json och gör följande ändring

{% code title="package.json" %}
```javascript
"scripts": {
  "start": "nodemon ./bin/www"
},
```
{% endcode %}

Testa sedan att starta servern igen och surfa till [localhost:3000](http://localhost:3000/)

### Eslint

Det kan vara väldigt användbart och praktiskt att låta ens IDE hjälpa en med fel och kodformatering. När vi kodar Javascript så finns det ett paket som heter [Eslint](https://eslint.org/) som hjälper med detta. För att installera det så kör du följande kommando.

```text
npm install --save-dev eslint
```

Vi kallar på npm, med install som kommando. Till det så ger vi npm parametern --save-dev som gör att paketet sparas i package.json under dev dependencies\(kolla\). Sist så anger vi namnet på paketet. För att slutföra installationen av Eslint så behöver vi skapa en konfiguration för det, kör.

```text
eslint --init
```

Svara som följer.

```text
? How would you like to use ESLint? To check syntax, find problems, and enforce code style
? What type of modules does your project use? CommonJS (require/exports)
? Which framework does your project use? None of these
? Does your project use TypeScript? No
? Where does your code run? Browser, Node
? How would you like to define a style for your project? Use a popular style guide
? Which style guide do you want to follow? Standard: https://github.com/standard/standard
? What format do you want your config file to be in? JavaScript
```

Välj sedan att installera paketen med npm, när den frågar.

Det har nu skapats en fil som heter .eslintrc.js som innehåller dina valda inställningar. För att läsa mer om Javascript standard style så kolla [Standardjs](https://standardjs.com/rules.html). Du kan nu lägga till och ändra reglerna i den. För att lägga till formateringsregler så ändrar du under rules i filen. Till exempel brukar jag lägga till att den alltid ska ge fel på avsaknaden av semikolon. Jag har tidigare också kört med fyra spaces som indentering, men på grund av Javascript försöker jag byta till två.

{% code title="eslintrc.js" %}
```javascript
rules: {
  // "indent": ["error", 4],
  "semi": ["error", "always"] // fel om det saknas semikolon, personlig preferens
}
```
{% endcode %}

## Lär känna din app

Nu är vår setup i stort sett klar för att börja ändra på vår app. Vi har installerat de paket som krävs och vi kan köra vår server. Nästa steg är att titta lite på Express filstruktur och hur du arbetar med det. Efter installationen så ser Express filstruktur ut såhär

```text
bin/
  www
node_modules/
public/
  images/
  javascript/
  stylesheets/
routes/
  index.js
  users.js
views/
  error.pug
  index.pug
  layout.pug
app.js
package.json
```

I roten finns app.js och package.json samt en del andra konfigurationsfiler. När du startar servern så kör npm start scriptet från package.json. Det kör i sin tur www filen från bin/ mappen som sedan startar app.js från rooten.

app.js laddar in servers routes från routes/ foldern, de tar i sin tur själva html/viewen från views/ mappen och visar detta. Statiskt innehåller finns i public/ mappen.

### Routes

Routerna som existerar är index och users. Index hanterar anrop till / och users till /users, inte helt otippat kanske. Detta system låter oss strukturera koden och hur vi hanterar applikationens rutter. Koden för index routern ser ut som följer, på [git](https://github.com/jensnti/wsp1-node/blob/ac1733d144ed049550e30fa2a711ae876ef9c3cd/routes/index.js)

{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/', function (req, res, next) {
  // render view med data
  res.render('index', { data: 'Data jag vill skicka till sidan' });
});
```
{% endcode %}

Själva views laddningen sker genom ett middleware i app.js som sätter path till views samt vilken motor som ska användas. Det finns många olika middleware av olika typer och det är en viktig del i Express funktion. Enkelt sagt så är middleware insticksprogram som gör något för Express och vår webbserver.

Om du vill kolla koden innan jag började ändra så mycket i projektet så kolla igenom repots [commit historik](https://github.com/jensnti/wsp1-node/commits/master). De commits som ungefär visar starten är [följande](https://github.com/jensnti/wsp1-node/tree/ac1733d144ed049550e30fa2a711ae876ef9c3cd), detta för att jag gjorde en del ändringar och bytte view-motor till Pug efter att jag kört generatorn.

