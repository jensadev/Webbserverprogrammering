---
description: Webbserverprogrammering med Node och Express
---

# Introduktion

## Vad är Node?

**Node.js (Node)** är en server. Beskrivningen på [nodejs.org](https://nodejs.org) kräver dock sin förklaring.

> As an asynchronous event-driven JavaScript runtime, Node.js is designed to build scalable network applications.

Node är byggt på en javascriptmotor ([Chromes V8](https://v8.dev)) för att kunna använda **Javascript** på en server. Node kan fungera som en en webbserver och fyller samma roll som webbservern Apache i [LAMP](../utvecklarmiljo/wsl.md#lamp-server).

## Installation och förberedelse

Den här guiden utgår från materialet kring [Windows Subsystem for Linux (WSL)](../utvecklarmiljo/wsl.md). Den [Node ](https://nodejs.org)version som kommer att användas är 14, vilket är Nodes **Long Term Support (LTS)** version. För att installera version 14 av Node går det inte att köra apt install node direkt.

{% tabs %}
{% tab title="Bash" %}
```bash
sudo apt update
sudo apt upgrade

sudo apt install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -

sudo apt install nodejs
```
{% endtab %}
{% endtabs %}

Kontrollera sedan den installerade versionen av Node, bör vara 14.x.

{% tabs %}
{% tab title="Bash" %}
```bash
node --version
```
{% endtab %}
{% endtabs %}

Med node följer **Node Package Manager (npm),** bör vara 6.x.

{% tabs %}
{% tab title="Bash" %}
```bash
npm --version
```
{% endtab %}
{% endtabs %}

Nästa steg är att installera **Express** och Express generator. [Express](https://expressjs.com) är ett minimalistiskt ramverk för Node. Express underlättar skapandet av en webbserver med Node. Express kommer att installeras globalt på systemet, något som kräver sudo.

{% tabs %}
{% tab title="Bash" %}
```bash
sudo npm install -g express express-generator
```
{% endtab %}
{% endtabs %}

Kontrollera sedan att det fungerade med.

{% tabs %}
{% tab title="Bash" %}
```bash
express --help
```
{% endtab %}
{% endtabs %}

### Ett första projekt

För att skapa grunden till ett webbprojekt så används Express generator.

{% hint style="info" %}
Var alltid väldigt noga med att köra kommandon från rätt mapp. Skapa inte Git-repon eller Express-projekt i c:\\
{% endhint %}

&#x20;Skapa en mapp för projektet, gå in i mappen och kör Express.

{% tabs %}
{% tab title="Bash" %}
```bash
mkdir PROJEKTNAMN
cd PROJEKTNAMN
express --no-view --css sass --git
```
{% endtab %}
{% endtabs %}

Express generator skapar med detta kommando grund-strukturen för en app. Express generator körs med parametrarna view, css och git.&#x20;

* View väljer den motor (på engelska **template engine**), **templatmotor**, som Express anväder. I det här fallet väljer vi att inte använda en motor för detta. Då genereras statiska filer som default. Men vi kommer att installera och använda [Nunjucks](https://mozilla.github.io/nunjucks/) för detta.
* Css väljer att Express ska använda sig av [sass](https://sass-lang.com)/scss för projektets stilar.
* Git skapar en [.gitignore](https://help.github.com/en/github/using-git/ignoring-files) fil i projektet för att undvika att filer som inte ska finnas på Git hamnar där(node\_modules mappen bland annat).

Node-projekt kräver i allmänhet ett antal NPM-paket för att fungera. Express är inget undantag och för att slutföra installationen behöver NPM ladda ned de paket som Express kräver. Paketen för ett Node-projekt är alltid listade i filen **package.json**. Paketen för Express installationen är följande.

{% code title="package.json" %}
```javascript
"dependencies": {
    "cookie-parser": "~1.4.4",
    "debug": "~2.6.9",
    "express": "~4.16.1",
    "morgan": "~1.9.1",
    "node-sass-middleware": "0.11.0"
},
```
{% endcode %}

NPM installerar paket med följande kommando.

{% tabs %}
{% tab title="Bash" %}
```bash
npm install
```
{% endtab %}
{% endtabs %}

npm install skapar en fil som heter **package-lock.json**, filen innehåller information om alla paketen som installerats och de paket som de kräver för att fungera i sin tur. Om install-kommandot kördes utan fel (vid fel står det npm ERR!) så är servern redo.

{% tabs %}
{% tab title="Bash" %}
```bash
npm start
```
{% endtab %}
{% endtabs %}

Surfa sedan till [localhost:3000](http://localhost:3000). Node-servern är startad och kan besökas med värddatorns **ip-adress** och rätt **port** även från andra datorer.

{% hint style="info" %}
Port 3000 används ofta för att komma åt en utvecklar Node-servern. Detta skiljer sig från HTTP-standardporten som är 80.
{% endhint %}

### Nodemon

Nodemon är ett paket till Node som underlättar utvecklingen av projekt. Det bevakar och lyssnar efter ändringar i serverns filer. Om filerna sparas (ändras) så laddas Node om.

{% tabs %}
{% tab title="Bash" %}
```bash
sudo npm install -g nodemon
```
{% endtab %}
{% endtabs %}

Uppdatera package.json.

{% code title="package.json" %}
```javascript
"scripts": {
  "dev": "nodemon ./bin/www"
},
```
{% endcode %}

Starta sedan servern igen.

```bash
npm run dev
```

### Eslint

**Visual Studio Code (vscode)** kan användas tillsammans med en så kallad **linter** för att hitta fel och formatera kod. Eslint är en linter för Javascript. Det är väldigt praktiskt vid arbete med kod och hjälper dig som utvecklare att följa praxis och skapa kod av hög kvalité. För att kunna köra Eslint kan det behöva installeras globalt, det gör vi med -g parametern.

{% tabs %}
{% tab title="Bash" %}
```bash
sudo npm install -g eslint
```
{% endtab %}
{% endtabs %}

För att slutföra installationen av Eslint så behöver en konfigurationsfil skapas. Var noga med att vara i projektmappen när du kör kommandot.

{% tabs %}
{% tab title="Bash" %}
```bash
eslint --init
```
{% endtab %}
{% endtabs %}

Svara följande:

```
? How would you like to use ESLint? To check syntax, find problems, and enforce code style
? What type of modules does your project use? CommonJS (require/exports)
? Which framework does your project use? None of these
? Does your project use TypeScript? No
? Where does your code run? Browser, Node
? How would you like to define a style for your project? Use a popular style guide
? Which style guide do you want to follow? Standard: https://github.com/standard/standard
? What format do you want your config file to be in? JavaScript
```

NPM kommer sedan att fråga om paketen ska installeras, välj ja.

I projektets root har Eslint skapat en fil som heter **.eslintrc.js** med inställningarna. Mer information kring  Javascript standard style hittar du på [Standardjs](https://standardjs.com/rules.html).&#x20;

Eslintrc kan nu redigeras för att ändra inställningarna. För att till exempel ändra formateringsregler så finns de under rules. En sådan regel kan vara att visa fel när semi-kolon saknas.

{% code title="eslintrc.js" %}
```javascript
rules: {
  // "indent": ["error", 4],
  "semi": ["error", "always"] // fel om det saknas semikolon
}
```
{% endcode %}

Till sist så behöver du installera eslint **extension** (svenska **tillägg**) i Vscode. Extensions finns i sidomenyn.

![Extensions i Vscode.](../.gitbook/assets/extensions.png)

Sök efter eslint och installera följande tillägg.

![](../.gitbook/assets/eslint.png)

## Lär känna din app

Applikationens grunder är nu installerade tillsammans med ett par verktyg för att underlätta utvecklingen. Nästa steg är att titta på Express filstruktur och hur du arbetar med det. Filstrukturen i Express ser ut såhär efter installationen.

```
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
app.js
package.json
```

I roten finns app.js och package.json samt en del andra konfigurationsfiler. App.js är Node-serverns startpunkt. Detta föregås av att start-scriptet i package.json utgår från filen www, i bin mappen, som i sin tur ladda app.js.

App.js laddar in serverns routes från routes/ foldern. När en **route** laddas efter ett anrop till servern så viser de i sin tur HTML-templaten från views/ mappen. Statiska filer finns i public/ mappen, såsom css och bilder.

### Routes

En route är den väg en request tar i din applikation. Användaren efterfrågar en resurs och i routen så bestämmer du vad som ska ske då.

Routerna som skapas av Express generator är index och users. Index hanterar anrop till / och users till /users. Applikationens router laddas och kopplas till en adress i app.js.&#x20;

{% code title="app.js" %}
```javascript
const indexRouter = require('./routes/index');
const usersRouter = require('./routes/users');
// ... //
app.use('/', indexRouter);
app.use('/users', usersRouter);
```
{% endcode %}

Routerna innehåller i sin tur koden för att hantera anropen. Detta system skapar struktur för koden. Koden för index-routen ser ut såhär, på [Git](https://github.com/jensnti/wsp1-node/blob/ac1733d144ed049550e30fa2a711ae876ef9c3cd/routes/index.js).

{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/', function (req, res, next) {
  // render view med data
  res.render('index', { data: 'Data jag vill skicka till sidan' });
});
```
{% endcode %}

En view laddas med ett så kallat **middleware** i app.js. Det sätter en **path** till views-mappen och bestämmer vilken templatmotor som ska användas. Det finns ett stort antal middleware med olika funktioner och det är en viktig del av Express. Enkelt sagt så är middleware insticksprogram som utökar Express funktion.

### En egen route

För att skapa en route så kan vi redigera filen index.js i routes mappen. Kopiera GET routen för / och klistra in den efter den existerande routen. Routen vi skapar är för en GET request till /test och den svarar med text.

{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/test', function (req, res, next) {
  // render view med data
  res.send('Detta är en ny testroute.');
});
```
{% endcode %}

### En till route

Routen ovan finns i index.js filen och är kopplad till / och /test. Prova nu att skapa en test router. Kopiera routes/index.js och döp om den till test.js.

Routen behöver sedan kopplas i app.js filen för att den ska kunna användas.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="app.js" %}
```javascript
const testRouter = require('./routes/test');
...
app.use('/test', testRouter);
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="routes/test.js" %}
```javascript
/* GET test page. */
router.get('/', function (req, res, next) {
  res.send('Denna route finns på /test/');
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

Prova sedan att surfa till [http://localhost:3000/test](http://localhost:3000/test), visas din nya route? Annars så behöver du felsöka detta. Har du kvar en /test route i index.js?
