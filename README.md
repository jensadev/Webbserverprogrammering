# README

## Introduktion Node.js och Express

Läs på [Gitbook, Webbserverprogrammering](https://jens-andreasson.gitbook.io/webbserverprogrammering/)

1. [Installation och förberedelse](./#installation-och-förberedelse)
   * [Ett första projekt](./#ett-första-projekt)
   * [Nodemon](./#nodemon)
   * [Eslint](./#eslint)
   * [Lär känna din app](./#lär-känna-din-app)
     * [Routes](./#routes)
2. [Hur funkar det?](./#hur-funkar-det)
   * [Pug](./#pug)
     * [Layout](./#layout)
     * [Index](./#index)
     * [Nav](./#nav)
     * [Users](./#users)
   * [Sass](./#sass)
     * [Style.sass](./#stylesass)
   * [Design](./#design)

## Installation och förberedelse

Du behöver köra Ubuntu under WSL. Vi kommer att köra Node version 12, vilket är deras LTS\(long term support\) version. För att installera detta behöver vi göra lite annorlunda än vanligt med ubuntu, detta för att standardversion för ubuntu är mycket äldre.

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

Du bör även ha npm\(node package manager\) installerat nu, kör\(bör vara 6.x\)

```text
npm --version
```

Nu när vi har Node installerat tillsammans med npm så kan vi installera express. [Express](https://expressjs.com/) är ett minimalistiskt ramverk för node. Express låter oss skapa routes och annat för vår applikation. Vi kommer även att installera en generator för Express för att förenkla vår setup\(scaffolding\). Vi kommer att installera Express globalt på systemet, därför kommer du att behöva köra följande som sudo.

```text
sudo npm i -g express express-generator
```

För att kolla om det gick som tänkt så kan du köra

```text
express --help
```

### Ett första projekt

För att skapa grunden för ett projekt så kör du enklast express i den mapp du arbetar i\(var noga\). Skapa en mapp för ditt projekt, gå in i den\(cd\) och kör express

```text
mkdir PROJEKTNAMN
cd PROJEKTNAMN
express --view=pug --css sass --git
```

Detta ger oss grunden för en vår app. Vi kör kommandot med tilläggen view,css och git. Detta låter oss välja vilken view motor vi vill att express ska använda. I det här fallet så är vår view [pug](https://pugjs.org/). Vi säger även till express att vi ska använda sass för css. Slutligen så skapar express en .gitignore fil åt oss så att vi inte laddar upp saker som inte har något på git att göra\(som node\_modules mappen\). För att färdigställa installationen så behöver du slutligen installera de paket som express använder. Paketen är listade i filen package.json som npm använder för att hålla koll på vilka paket som din app använder. Paketen är

```javascript
// File: package.json
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

För att installera paketen kör du

```text
npm install
```

Om kommandot kördes utan några fel \(då står det npm ERR!\) så kan du nu starta din server med

```text
npm start
```

Surfa sedan till [localhost:3000](http://localhost:3000) och du är igång!

### Nodemon

Ett väldigt praktiskt tillägg till node är nodemon, det låter oss starta en server som automatiskt startar om när vi ändrar på filer. Detta gör att vi slipper sitta och starta om node såfort vi ändrar i någon fil.

```text
npm install --save-dev nodemon
```

Sedan redigerar vi package.json och gör följande ändring

```javascript
// File: package.json
"scripts": {
  "start": "nodemon ./bin/www"
},
```

Testa sedan att starta servern igen och surfa till [localhost:3000](http://localhost:3000)

### Eslint

Det kan vara väldigt användbart och praktiskt att låta ens IDE hjälpa en med fel och kodformatering. När vi skriver javascript tillsammans med Node så finns det ett paket som heter [eslint](https://eslint.org/) som hjälper med detta. För att installera det så kör du följande kommando

```text
npm install --save-dev eslint
```

Vi kallar på npm, med install som kommando. Till det så ger vi npm parametern --save-dev som gör att paketet sparas i package.json under dev dependencies. Sist så anger vi namnet på paketet. För att slutföra installationen av eslint så behöver vi skapa en konfiguration. Kör

```text
eslint --init
```

Svara som följer

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

Välj sedan att installera paketen med npm.

Det har nu skapats en fil som heter .eslintrc.js som innehåller dina valda inställningar. För att läsa mer om Javascript standard style så kolla [Standardjs](https://standardjs.com/rules.html). Du kan nu lägga till och ändra reglerna i den. För min egen del så lägger jag alltid följande formateringsregler.

```javascript
// File: .eslintrc.js
rules: {
  // "indent": ["error", 4], // fel vid annan indentering än 4 spaces, 2 är standardjs
  "semi": ["error", "always"] // fel om det saknas semikolon, personlig preferens
}
```

### Lär känna din app

Nu är vår setup i stort sett klar för att börja ändra på vår app. Vi har installerat de paket som krävs och vi kan köra vår server. Nästa steg är att titta lite på express filstruktur och hur du arbetar med det. Efter installationen så ser express filstruktur ut såhär

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

Routerna som existerar är index och users. Index hanterar anrop till / och users till /users, inte helt otippat. Detta system låter oss strukturera koden och hur vi hanterar applikationens rutter. Koden för index routern ser ut som följer, på [git](https://github.com/jensnti/wsp1-node/blob/ac1733d144ed049550e30fa2a711ae876ef9c3cd/routes/index.js)

```javascript
// File: routes/index.js
/* GET home page. */
router.get('/', function (req, res, next) {
  // render view med data
  res.render('index', { data: 'Data jag vill skicka till sidan' });
});
```

Själva views laddningen sker genom ett middleware i app.js som sätter path till views samt vilken motor som ska användas. Det finns många olika middleware av olika typer och det är en viktig del i Express funktion. Enkelt sagt så är middleware insticksprogram som gör något för Express och vår webbserver.

Om du vill kolla koden innan jag började ändra så mycket i projektet så kolla igenom [commit historiken](https://github.com/jensnti/wsp1-node/commits/master). De commit som ungefär visar starten är [följande](https://github.com/jensnti/wsp1-node/tree/ac1733d144ed049550e30fa2a711ae876ef9c3cd), detta för att jag gjorde en del ändringar och bytte view-motor till pug.

## Hur funkar det?

För att titta på hur det fungerar så ska vi börja ändra i koden. För att ändra på vår html och testa hur det fungerar så ska vi skapa en meny som gör att vi kan komma åt andra sidor. Express generator kommer med en router för en user sida som endast svarar med en resurs. Vi kommer att byta ut den routen för att svara med en user sida, så att vi kan testa och använda navigationen vi skapar.

### Pug

Express stöder ett flertal olika templat-motorer, i det här exemplet så kommer vi att arbeta med en som heter Pug. Varför pug, mest för att jag har använt den tidigare och kört den i kursen. Det har fungerat bra och de flesta har förstått sig på den. Pug är snarlikt html och relativt enkelt att komma igång med, syntaxen är förhållandevis enkel och det gör det enkelt att komma igång.

Innehållet på sidan struktureras med indenteringar\(bra för att öva kodstruktur\) och html taggarna skrivs med namn, men de behöver inte avslutas. Avslutandet av taggen baseras på indenteringen. Så om jag vill lägga element i ett annat så gör jag det med indentering. Pug stödjer variabler, iteration och mixins\(funktioner typ\) bland annat.

```text
div#idname
  h1.classname Rubrik
  p.class1.class2 Brödtext
    | Fortsatt text i p elementet ovan.
  p Lorem...
    a(href='#') Länk i p texten
```

Jag ska inte upprepa [dokumentationen](https://pugjs.org/api/getting-started.html) här, utan det är bäst att du tittar igenom det och sedan använder den vid behov. Det finns även ett antal extensions för Visual studio code för den som vill ha stöd i sin IDE.

En start kan vara att konvertera redan färdiga sidor till pug, det finns ett otal konverterare som du kan hitta genom [google](https://www.google.com/search?q=html+to+pug&oq=html+to+pug&aqs=chrome..69i57j0l6j69i60.4848j0j7&sourceid=chrome&ie=UTF-8).

#### Layout

Den struktur som vi kommer att använda för projektets views med pug är att vi utgår från filen layout.pug. Denna fil anropar vi sedan genom våra andra layout filer.

Så i index.pug så anropar vi layout.pug genom att skriva

```text
extends layout
```

Layoutfilen är projektets html bas, här inkluderar du alla delar som du vill att samtliga sidor ska ärva genom extends. Så för att starta behöver vi allt annat som vi vanligtvis inkluderar i en validerande html5 sida. Öppna filen som skapats och kolla hur det ser ut och jämför med följande.

```text
//- views/layout.pug
doctype html
html(lang='sv')
  head
    meta(name='viewport' content='width=device-width,initial-scale=1.0')
    meta(charset='utf-8')
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
```

Jag har utvecklat meta delen något samt lagt till språk-attributet på html elementet.

#### Index

När vi nu kollar på `views/index.pug` så är det viktigt att vi tittar på `routes/index.js` tillsammans med dem. Då det är routes filen som kallar på `res.render()` funktionen för att visa den view vi efterfrågar.

```javascript
// File: routes/index.js
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
```

Om vi nu tittat på där [render](https://expressjs.com/en/api.html#res.render) funktionen kallas så ser vi att den inkluderar först den view vi ska använda och sedan så skickar den med ett objekt till viewen. I det här fallet så innehåller objektet parametern title med värdet Express.

```text
//- views/index.pug
extends layout

block content
  main
    h1= title
    p Welcome to #{title}
```

Här ser vi även på hur Pug hanterar title värdet som vi skickade till templaten. Läs mer om hur du jobbar med template locals [här](https://pugjs.org/language/interpolation.html).

#### Nav

En av fördelarna med Pug är att vi kan inkludera och extenda våra templater så vi kan återanvända kod. Det leder till enklare utveckling och mindre fel. Ett bra exempel på något som vi kan återanvända är en navigation på en webbsida. Så vi ska nu skapa och lägga till en top-navigation. Börja med att lägga till följande kod i `views/layout.pug` efter body taggen\(behåll det efterfölljande content blocket\).

```text
//- views/layout.pug
body
block nav
  include nav.pug
```

Här anger vi ett nav block följt av en include där vi läser in filen `nav.pug`. Läs mer om Pugs [arv](https://pugjs.org/language/inheritance.html). När vi nu laddar vår layout så kommer innehållet i nav.pug inkluderas innan vårt content. Nästa steg blir att skapa filen och i den kommer vi att skapa ett nav element med en lista i.

```text
//- views/nav.pug
nav
  ul
    li
      a(href='/') Home
    li
      a(href='/users') Users
```

Spara filerna och ladda om sidan, du kommer nu se att `nav.pug` inkluderas och att du kan följa länkarna. Klickar du på users så kommer enbart Express `res.send` resultat visas.

#### Users

Vi ska nu testa att ändar den route som express har för users. I `routes/users.js` så ska vi ändra routes respons så att vi skickar en view kallad users.

```javascript
// routes/users.js
res.render('users', {});
```

När vi skapat routen så behöver vi sedan skapa en views fil för detta, döp den till `views/users.pug`. Du kan basera denna fil på innehållet i index viewen.

```text
//- views/users.pug
extends layout

block content
  main
    h1= title
```

Testa nu att surfa runt på din sida, förhoppningsvis fungerar det.

För att visa vad vi kan göra med Pug tillsammans med express så ska vi nu skicka med data för ett antal users och sedan visa det med vår uppgraderade view. Börja med att skapa en array med några användare i routes filen.

```javascript
// routes/users.js
res.render('users', {'users': ['Hans', 'Moa', 'Bengt', 'Frans', 'Lisa'] });
```

Så detta ger oss tillgång till denna data i `views/users.pug`. Om vi kollar på dokumentation för [iteration](https://pugjs.org/language/iteration.html) i Pugs manual så ser vi att det finns ett par exempel för hur detta kan göras. Vi kommer här att använda en iteration med formen, för varje user i users gör... Uppdatera filen med följande.

```text
//- views/users.pug
ul
  each user in users
    li= user
```

Vi skapar här en lista där vi lägger till ett li element för varje index i users. Ladda om sidan och se resultatet.

#### Footer

Du kan nu prova att lägga till en footer som ska inkluderas på varje sida, förfarandet är mer eller mindre detsamma som för navigationen. Skapa filen `views/footer.pug` och inkludera den från `views/layout.pug`. I filen skapar du ett footer element.

Med den grunden på plats så kan vi börja titta på att få det att se ut som något. För detta så kommer vi att arbeta med Sass.

### Sass

För projektet så kommer vi att skriva Sass för att förkompilera vår css, våra stilar.

Sass kan installeras med npm eller utan, för att installera paketet utan npm så går det att göra detta med apt under Ubuntu. Liknande finns förstås med Windows. Hursomhelst så kan det vara enklast att lägga till npm paketet. Vill du så kan du såklart lägga till det globalt med -g flaggan.

Projektet vi arbetar med nu har en middleware installerad för att kompilera .sass filerna till .css filer, det fungerar i överlag bra, men i det här projektet ville jag dela upp filerna något och använda sass `@Use` för att inkludera filer. Detta resulterade i ett kompileringsfel med sass middleware och fungerade inte. Av den anledningen installerade jag sass separat och skrev ett script kommando i projektets package.json för att kompilera min css. På grund av detta kan vi behöva installera sass.

```text
npm install --save-dev sass
```

Uppdatering till package.json's script del.

```javascript
// File:package.json
"scripts": {
  "start": "nodemon ./bin/www",
  "compile": "sass --watch public/stylesheets/"
},
```

Vi kan sedan köra npm run compile och den kommer att köra sass och söka efter eventuella ändringar i källfilerna. Så när vi sparar vår sass fil, då kompileras det till css.

Vi är nu redo att börja designa sidan. De första stilarna styr förhoppningsvis upp lite grundläggande användbarhet, som vi kan bygga vidare på. Läs mer om den här exempelsidans stilar under [Design](./#design).

#### style.sass

Syntaxen för att skriva sass skiljer något från vanlig css, men grunden är desamma. Det är tämligen likt Pug då det förlitar sig på korrekt indentering för att fungera och strukturera. Öppna dokumentet och skriv in följande.

```css
// File: public/stylesheets/style.sass
html
  height: 100%

body
  height: 100%
  display: flex
  flex-direction: column

nav, main, footer
  width: 80%
  margin: 0 auto

footer
  margin-top: auto

nav > ul
  list-style: none
  display: flex
  padding: 0

  > li
    padding-right: 16px
```

Detta ger dig lite grundläggande stilar som formaterar dina dokument. Du har en navigation, main och en footer. Placeringen av elementen sker med flexbox. Testa nu med att lägga till en egen font från [Google fonts](https://fonts.google.com/), då behöver du dels länka den i din layout, men även inkludera den i sass dokumentet.

När du lägger till fonten i ditt dokument så kan du med fördel skapa en variabel för detta, så att du kan återanvända fonten. I sass skriver du då.

```css
$font: font-family: 'Roboto', sans-serif

h1
  font-family: $font
```

Funkar det? Testa nu att skapa variabler för ett par färger på sidan. Passa på att styla user-sidans lista också. Fortsätt sedan här för att lära dig mer om [Sass](https://sass-lang.com/guide).

## Design

För projektet så skapade jag ett par skisser med figma, du hittar dem [här](https://www.figma.com/file/tngmvFgOZ96E1xHm9Igr9o/Webbserver-node?node-id=0%3A1). Design är svårt och ett evigt pillande fram och tillbaka mellan olika ställningstaganden. Under processen så märker en ofta också problem med designen, vilket kan vara både estetiska och tekniska. Men i detta arbetet så förbättrar vi produkten stegvis.

Som exempel så letade jag reda på ett färgschema jag gillade först, vilket påminde mig om något under vatten. Jag gillar det, men hur jag använder det på sidan får problem med kontrast. Så jag skruvade lite på hur kontrasterna, men inte så mycket som krävs av de testverktyg jag använder, eftersom jag tyckte jag förlorade känslan. Här gjorde jag en avvägning att behålla min design och inte skrota den på grund av testresultatet. Men det är viktigt att göra detta val tidigt, så att du inte behöver ändra färgerna på hela webbplatsen när du kodat färdigt, för att du aldrig kollade kontrasten. Här underlättar det väldigt mycket också om du använder Sass och tilldelar färgerna till variabler. Du kan dessutom använda Sass [funktioner](https://sass-lang.com/documentation/modules/color) för att manipulera färgerna.

Just tanken om något under vatten ledde till bilden med bubblorna, något som jag skapade i Adobe Illustrator och sparade som SVG. Jag letade upp ett par ikoner och NTI logotypen, sparade dessa som SVG och la till. ![Bubbles image](https://raw.githubusercontent.com/jensnti/wsp1-node/master/public/images/bubbles-v2.svg)

När jag arbetade med den mycket långa titeln, Webbserverprogrammering så fick jag tekniska problem. Det visade sig att ett väldigt långt ord som inte kan avstavas av webbläsaren påverkar hur hela sidan ritas ut när det finns tillsammans med `meta viewport scale`. I det här fallet påverkade det hur resten av sidan ritades ut och resultatet blev hemskt. Lösningen blev att lägga till ett bindestreck mellan Webbserver och programmering, Webbserver-programmering. Inte vad jag kanske önskat, men enklaste och bästa lösningen. Med HTML och CSS så försöker vi få text att se ut som något, vilket inte alltid fungerar som vi tänkt oss. Ibland är det inte ens möjligt, men med testning kan vi åtminstone upptäcka problem som vi på ett eller annat sätt får lösa.

Det här är exempel på avvägningar som behöver göras oavsett om en har skisser att utgå från eller inte. Men att utgå från en skiss och en ide, ger dig ett stöd som alltid underlättar. Att formulera din ide och slipa på den är en del i en process som fortsätts när du kodar din html/css och att göra det i flera steg leder alltid till ett bättre slutresultat.

### Från html till webbplats

På det stora hela så handlar det om att stegvis börja skapa sin skiss med hjälp av css stilar. Några viktiga saker att ha i åtanke är att inte försöka designa utan innehåll. Placeringen av element i html är beroende av andra element, så ditt innehåll kommer alltid påverka placeringen. Det andra är att det är väldigt viktigt att strukturen är korrekt och att elementen är korrekt stängda. Annars blir det väldigt svårt med stilarna och det kommer förmodligen resultera i att det inte blir som du önskar. För att göra detta så rekommenderar jag att du [validerar](https://validator.nu/) regelbundet.

Undvik att positionera absolut och tänk alltid på att ändra storleken på din webbläsare, för du kan inte förutsätta att användaren alltid kör samma upplösning som du.

Kom även ihåg att inte fastna i evigheter på detaljer innan du ens har någon design.

## Markdown

[Markdown syntax](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)

