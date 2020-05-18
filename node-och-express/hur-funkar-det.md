# Hur funkar det?

För att titta på hur det fungerar så ska vi börja ändra i koden. För att ändra på vår html och testa hur det fungerar så ska vi skapa en meny som gör att vi kan komma åt andra sidor. Express generator kommer med en router för en user sida som endast svarar med en resurs. Vi kommer att byta ut den routen för att svara med en user sida, så att vi kan testa och använda navigationen vi skapar.

## Pug

Express stöder ett flertal olika templat-motorer, i det här exemplet så kommer vi att arbeta med en som heter Pug. Varför Pug då, mest för att jag har använt den tidigare och kört den i kursen. Det har fungerat bra och de flesta har förstått sig på den. Pug är snarlikt html och relativt enkelt att komma igång med, syntaxen är förhållandevis enkel och det gör det enkelt att komma igång.

Innehållet på sidan struktureras med indenteringar\(bra för att öva kodstruktur\) och html taggarna skrivs med namn, men de behöver inte avslutas. Avslutandet av taggen baseras på indenteringen. Så om jag vill lägga element i ett annat så gör jag det med indentering. Pug stödjer variabler, iteration och mixins\(funktioner typ\) bland annat.

```text
div#idname
  h1.classname Rubrik
  p.class1.class2 Brödtext
    | Fortsatt text i p elementet ovan.
  p Lorem...
    a(href='#') Länk i p texten
```

Jag ska inte upprepa [dokumentationen](https://pugjs.org/api/getting-started.html) här, utan det är bäst att du tittar igenom det och sedan använder den vid behov. Det finns även ett antal extensions för [Visual studio code](https://code.visualstudio.com/) för den som vill ha stöd i sin IDE.

En start kan vara att konvertera redan färdiga sidor till pug, det finns ett otal konverterare som du kan hitta genom [Google](https://www.google.com/search?q=html+to+pug&oq=html+to+pug&aqs=chrome..69i57j0l6j69i60.4848j0j7&sourceid=chrome&ie=UTF-8).

### Layout

Den struktur som vi kommer att använda för projektets views med Pug är att vi utgår från filen layout.pug. Denna fil anropar vi sedan genom våra andra layout filer.

Så i index.pug så anropar vi layout.pug genom att skriva

```text
extends layout
```

Layoutfilen är projektets html bas, här inkluderar du alla delar som du vill att samtliga sidor ska ärva genom extends. Så för att starta behöver vi allt annat som vi vanligtvis inkluderar i en validerande html sida. Öppna filen som skapats och kolla hur det ser ut och jämför med följande.

{% code title="views/layout.pug" %}
```markup
doctype html
html(lang='sv')
  head
    meta(name='viewport' content='width=device-width,initial-scale=1.0')
    meta(charset='utf-8')
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
```
{% endcode %}

Jag har utvecklat meta delen något samt lagt till språk-attributet på html elementet.

### Index

När vi nu kollar på `views/index.pug` så är det viktigt att vi tittar på `routes/index.js` tillsammans med dem. Då det är routes filen som kallar på `res.render()` funktionen för att visa den view vi efterfrågar.

{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
```
{% endcode %}

Om vi nu tittat på där [render](https://expressjs.com/en/api.html#res.render) funktionen kallas så ser vi att den inkluderar först den view vi ska använda och sedan så skickar den med ett objekt till viewen. I det här fallet så innehåller objektet parametern title med värdet Express.

{% code title="views/index.pug" %}
```text
extends layout

block content
  main
    h1= title
    p Welcome to #{title}
```
{% endcode %}

Här ser vi även på hur Pug hanterar title värdet som vi skickade till templaten. Läs mer om hur du jobbar med template locals [här](https://pugjs.org/language/interpolation.html).

### Nav

En av fördelarna med Pug är att vi kan inkludera och extenda våra templater så vi kan återanvända kod. Det leder till enklare utveckling och mindre fel. Ett bra exempel på något som vi kan återanvända är en navigation på en webbsida. Så vi ska nu skapa och lägga till en top-navigation. Börja med att lägga till följande kod i `views/layout.pug` efter body taggen\(behåll det efterfölljande content blocket\).

{% code title="views/layout.pug" %}
```text
body
  block nav
    include nav.pug
```
{% endcode %}

Här anger vi ett nav block följt av en include där vi läser in filen `nav.pug`. Läs mer om [Pugs arv](https://pugjs.org/language/inheritance.html). När vi nu laddar vår layout så kommer innehållet i nav.pug inkluderas innan vårt content. Nästa steg blir att skapa filen och i den kommer vi att skapa ett nav element med en lista i.

{% code title="views/nav.pug" %}
```text
nav
  ul
    li
      a(href='/') Home
    li
      a(href='/users') Users
```
{% endcode %}

Spara filerna och ladda om sidan, du kommer nu se att `nav.pug` inkluderas och att du kan följa länkarna. Klickar du på users så kommer enbart Express `res.send` resultat visas.

### Users

Vi ska nu testa att ändra den route som Express använder för users. I `routes/users.js` så ändrar vi routens respons så att vi skickar en view kallad users.

{% code title="routes/users.js" %}
```javascript
res.render('users', {});
```
{% endcode %}

När vi skapat routen så behöver vi sedan skapa en views fil för detta, döp den till `views/users.pug`. Du kan basera denna fil på innehållet i index viewen.

{% code title="views/users.pug" %}
```text
extends layout

block content
  main
    h1= title
```
{% endcode %}

Testa nu att surfa runt på din sida, förhoppningsvis fungerar det.

För att visa vad vi kan göra med Pug tillsammans med express så ska vi nu skicka med data för ett antal users och sedan visa det med vår uppgraderade view. Börja med att skapa en array med några användare i routes filen.

{% code title="routes/users.js" %}
```javascript
res.render('users', {'users': ['Hans', 'Moa', 'Bengt', 'Frans', 'Lisa'] });
```
{% endcode %}

Så detta ger oss tillgång till denna data i `views/users.pug`. Om vi kollar på dokumentation för [iteration](https://pugjs.org/language/iteration.html) i Pugs manual så ser vi att det finns ett par exempel för hur detta kan göras. Vi kommer här att använda en iteration med formen, för varje user i users gör... Uppdatera filen med följande.

{% code title="views/users.pug" %}
```text
ul
  each user in users
    li= user
```
{% endcode %}

Vi skapar här en lista där vi lägger till ett li element för varje index i users. Ladda om sidan och se resultatet.

### Footer

Du kan nu prova att lägga till en footer som ska inkluderas på varje sida, förfarandet är mer eller mindre detsamma som för navigationen. Skapa filen `views/footer.pug` och inkludera den från `views/layout.pug`. I filen skapar du ett footer element.

Med den grunden på plats så kan vi börja titta på att få det att se ut som något. För detta så kommer vi att arbeta med Sass.

## Sass

För projektet så kommer vi att skriva Sass för att förkompilera vår css, våra stilar.

Sass kan installeras med npm eller utan, för att installera paketet utan npm så går det att göra detta med apt under Ubuntu. Liknande finns förstås med Windows. Hursomhelst så kan det vara enklast att lägga till npm paketet. Vill du så kan du såklart lägga till det globalt med -g flaggan.

Projektet vi arbetar med nu har en middleware installerad för att kompilera .sass filerna till .css filer, det fungerar i överlag bra, men i det här projektet ville jag dela upp filerna något och använda [Sass `@Use`](https://sass-lang.com/documentation/at-rules/use) för att inkludera filer. Detta resulterade i ett kompileringsfel med Sass middleware och fungerade inte. Av den anledningen installerade jag Sass separat och skrev ett script kommando i projektets package.json för att kompilera min css. På grund av detta kan vi behöva installera Sass.

```text
npm install --save-dev sass
```

Uppdatering till package.json, i script delen.

{% code title="package.json" %}
```javascript
"scripts": {
  "start": "nodemon ./bin/www",
  "compile": "sass --watch public/stylesheets/"
},
```
{% endcode %}

Vi kan sedan köra npm run compile och den kommer att köra Sass och söka efter eventuella ändringar i källkodsfilerna. Så när vi sparar vår Sass fil, då kompileras det till css.

Vi är nu redo att börja designa sidan. De första stilarna styr förhoppningsvis upp lite grundläggande användbarhet, som vi kan bygga vidare på. Läs mer om den här exempelsidans stilar under [Design]().

### style.sass

Syntaxen för att skriva Sass skiljer något från vanlig css, men grunden är desamma. Det är tämligen likt Pug då det förlitar sig på korrekt indentering för att fungera och strukturera. Öppna dokumentet och skriv in följande.

{% code title="public/stylesheets/style.sass" %}
```css
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
{% endcode %}

Detta ger dig lite grundläggande stilar som formaterar dina dokument. Du har en navigation, main och en footer. Placeringen av elementen sker med flexbox. Testa nu med att lägga till en egen font från [Google fonts](https://fonts.google.com/), då behöver du dels länka den i din layout, men även inkludera den i Sass dokumentet.

När du lägger till fonten i ditt dokument så kan du med fördel skapa en variabel för detta, så att du kan återanvända fonten. I filen skriver du då.

{% code title="public/stylesheets/style.sass" %}
```css
$font: font-family: 'Roboto', sans-serif

h1
  font-family: $font
```
{% endcode %}

Funkar det? Testa nu att skapa variabler för ett par färger på sidan. Passa på att styla user-sidans lista också. Fortsätt sedan här för att lära dig mer om [Sass](https://sass-lang.com/guide).

