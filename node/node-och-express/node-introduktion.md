# Introduktion

## Vad är Node?

**Node.js \(Node\)** är en server. Beskrivningen på [nodejs.org](https://nodejs.org/) kräver dock sin förklaring.

> As an asynchronous event-driven JavaScript runtime, Node.js is designed to build scalable network applications.

Node är byggt på en javascriptmotor för att kunna använda javascript på en server. Det funkar utmärkt för en webbserver och är ett alternativ till LAMP server.

## Installation och förberedelse

Den här guiden utgår från materialet kring [**Windows Subsystem for Linux \(WSL\)**](../../utvecklarmiljo/wsl.md). Den [Node ](https://nodejs.org/)version som kommer att användas är 12, vilket är Nodes **Long Term Support \(LTS\)** version. För att installera version 12 av Node kan vi inte använda apt install node direkt.

```text
sudo apt update
sudo apt upgrade

sudo apt install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

sudo apt install nodejs
```

Kontrollera sedan den installerade versionen av Node, bör vara 12.x.

```text
node --version
```

Med node följer **Node Package Manager \(npm\),** bör vara 6.x.

```text
npm --version
```

Nästa steg är att installera **Express** och Express generator. [Express](https://expressjs.com/) är ett minimalistiskt ramverk för Node. Express underlättar skapandet av en webbserver med Node. Express kommer att installeras globalt på systemet, något som kräver sudo.

```text
sudo npm i -g express express-generator
```

Kontrollera sedan att det fungerade med.

```text
express --help
```

### Ett första projekt

För att skapa grunden till ett webbprojekt så används Express generator.

{% hint style="info" %}
Var alltid väldigt noga med att köra kommandon från rätt mapp. Skapa inte Git-repon eller Express-projekt i c:\
{% endhint %}

 Skapa en mapp för projektet, gå in i mappen och kör Express.

```text
mkdir PROJEKTNAMN
cd PROJEKTNAMN
express --view=pug --css sass --git
```

Express generator skapar med detta kommando grund-strukturen för en app. Express generator körs med parametrarna view, css och git. 

* View väljer den motor, **templatsystem**, som Express anväder. I det här fallet Pug.
* Css väljer att Express ska använda sig av sass för projektets stilar.
* Git skapar en .gitignore fil i projektet för att undvika att filer som inte ska finnas på Git hamnar där\(node\_modules mappen bland annat\).

Node-projekt kräver i allmänhet ett antal NPM-paket för att fungera. Express är inget undantag och för att slutföra installationen behöver NPM ladda ned de paket som Express kräver. Paketen för ett Node-projekt är alltid listade i filen **package.json**. Paketen för Express installationen är följande.

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

NPM installerar paket med följande kommando.

```text
npm install
```

När NPM kör install så skapas också en **package-lock.json**, filen innehåller information om alla paketen som installerats och de paket som de kräver för att fungera i sin tur. Om install-kommandot kördes utan fel \(vid fel står det npm ERR!\) så är servern redo.

```text
npm start
```

Surfa sedan till [localhost:3000](http://localhost:3000/). Node-servern är startade och kan nu besökas med värddatorns **ip-adress** och rätt **port**.

{% hint style="info" %}
Port 3000 används ofta för att komma åt en utvecklar Node-servern. Detta skiljer sig från HTTP-standardporten som är 80.
{% endhint %}

### Nodemon

Nodemon är ett paket till Node som underlättar utvecklingen av projekt. Det bevakar och lyssnar efter ändringar i serverns filer. Om filerna sparas \(ändras\) så laddas Node om.

```text
npm install --save-dev nodemon
```

Uppdatera package.json

{% code title="package.json" %}
```javascript
"scripts": {
  "start": "nodemon ./bin/www"
},
```
{% endcode %}

Starta sedan servern igen.

### Eslint

Den utvecklarmiljö som används

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

{% hint style="info" %}
Om du vill kolla koden innan jag började ändra så mycket i det här projektet så kolla igenom repots [commit historik](https://github.com/jensnti/wsp1-node/commits/master). De commits som ungefär visar starten är [följande](https://github.com/jensnti/wsp1-node/tree/ac1733d144ed049550e30fa2a711ae876ef9c3cd), detta för att jag gjorde en del ändringar och bytte view-motor till Pug efter att jag kört generatorn.
{% endhint %}

