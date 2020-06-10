# Hur funkar det?

Det här avsnittet handlar om hur **Express** fungerar. För att förstå det så kommer du att skriva om delar av startkoden som Express generator skapat. Det första steget är att skapa en meny för sidan som kan återanvändas. Det låter oss prova att arbeta med Express **routes**, **templater** och presentation. Express generator skapar en user **router** som endast svarar med en resurs. Users routen kommer att modifieras för att presentera en sida vilken även kommer att innehålla menyn.

## Pug

Express stöder ett flertal olika **templatmotorer** \(på engelska **template engine**\), den som används här är  [**Pug**](https://pugjs.org/). Pug är snarlikt html och relativt enkelt att komma igång med.

Innehållet på en sida med Pug struktureras med **indentering.** Text i början av en rad representerar generellt en **HTML** **tag**. Taggarna behöver inte stängas \(det sköter indenteringen\). För att **nästla** **element** indenteras de under andra taggar. Läs mer om [taggar](https://pugjs.org/language/tags.html) och [attribut](https://pugjs.org/language/attributes.html) i Pug.

Pug stöder **variabler**, **iteration** och **mixins**\(funktioner\) bland annat.

```text
div#idname
  h1.classname Rubrik
  p.class1.class2 Brödtext
    | Fortsatt text i p elementet ovan.
  p Lorem...
    a(href='#') Länk i p texten
```

Läs mer om Pug i [dokumentation](https://pugjs.org/)en och använd den vid behov. Det finns även ett antal **extensions** för [**Visual Studio Code**](https://code.visualstudio.com/) \(förkortat till **vscode**\) för att underlätta arbetet med Pug.

{% hint style="info" %}
Det går utmärkt att konvertera färdiga HTML-sidor till Pug, det finns flera verktyg för detta, [Google](https://www.google.com/search?q=html+to+pug&oq=html+to+pug&aqs=chrome..69i57j0l6j69i60.4848j0j7&sourceid=chrome&ie=UTF-8).
{% endhint %}

### Layout

Det här projektets view-struktur utgår från filen layout.pug. Underliggande sidor ärver **layout-filens** struktur för att skapa en färdig HTML-sida. Nyckel-ordet för detta är`extends`.

Filen index.pug ärver från layout.pug med

```text
extends layout
```

Layout-filen är projektets HTML-bas så i den filen behövs en validerande HTML grund. Öppna filen och redigera.

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

### Index

**Index-filen** är webbsidans startpunkt. Den **view** som presenteras av index-filen anropas av den **route** som är kopplat till index-routen. Route filen kallar `res.render()` funktionen för att visa den view som anges, i det här fallet index.

{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
```
{% endcode %}

I [render](https://expressjs.com/en/api.html#res.render) funktionen kallas först den view som ska användas, index, sedan bifogas ett **objekt** till view-filen. Objeketet innehåller här **egenskapen** title med Express som **värde**.

{% code title="views/index.pug" %}
```text
extends layout

block content
  main
    h1= title
    p Welcome to #{title}
```
{% endcode %}

I index-filen kan sedan det bifogade objektet användas för att dynamiskt ändra view-filen. Det kallas för template locals, läs mer om template locals [här](https://pugjs.org/language/interpolation.html).

### Nav

Med extends kan Pug återanvända kod. Det leder till enklare utveckling och mindre fel \([don't repeat yourself, DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)\). Ett bra exempel på detta är navigation på en webbsida. 

Du ska nu skapa en top-navigation. Det första steget är att skapa ett nav **block** i layout-filen efter body-taggen.

{% code title="views/layout.pug" %}
```text
body
  block nav
    include nav.pug
  block content
```
{% endcode %}

Här anger vi ett nav block följt av en include där vi läser in filen `nav.pug`. Läs mer om [Pugs arv](https://pugjs.org/language/inheritance.html). När vi nu laddar vår layout så kommer innehållet i `nav.pug` inkluderas innan vårt content. Nästa steg blir att skapa filen och i den kommer vi att skapa ett nav element med en lista i.

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

För att visa vad vi kan göra med Pug tillsammans med express så ska vi nu skicka med data för ett antal users och sedan visa det med vår uppgraderade view. Börja med att skapa en [array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) med några användare i routes filen.

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

{% hint style="info" %}
Detta är en uppgift med eget arbete.
{% endhint %}

Du kan nu prova att lägga till en footer som ska inkluderas på varje sida, förfarandet är mer eller mindre detsamma som för navigationen. Skapa filen `views/footer.pug` och inkludera den från `views/layout.pug`. I filen skapar du ett footer element.

* [ ] Skapa `footer.pug` fil
* [ ] Skapa element och innehåll
* [ ] Inkludera footer i `layout.pug`

Med den grunden på plats så kan vi börja titta på att få det att se ut som något. För detta så kommer vi att arbeta med Sass.

## Sass

För projektet så kommer vi att skriva Sass för att förkompilera vår css, våra stilar.

Sass kan installeras med npm eller utan, för att installera paketet utan npm så går det att göra detta med apt under Ubuntu. Liknande finns förstås med Windows. Hursomhelst så kan det vara enklast att lägga till npm paketet. Vill du så kan du såklart lägga till det globalt med -g flaggan.

Projektet vi arbetar med nu har en middleware installerad för att kompilera .sass filerna till .css filer. Så om du inte behöver inkludera flera Sass filer så behöver du inte ändra något mer. 

{% hint style="danger" %}
I det här projektet ville jag dela upp min Sass kod över flera filer. Detta görs med [`@Use`](https://sass-lang.com/documentation/at-rules/use) i Sass. Detta resulterade dock i ett kompileringsfel med Sass middleware och fungerade inte. Av den anledningen installerade jag Sass separat och skrev ett script kommando i projektets package.json för att kompilera min css. 
{% endhint %}

För att installera Sass separat så kör.

```text
npm install --save-dev sass
```

Uppdatera sedan `package.json` och i script delen lägger du till följande.

{% code title="package.json" %}
```javascript
"scripts": {
  "start": "nodemon ./bin/www",
  "compile": "sass --watch public/stylesheets/"
},
```
{% endcode %}

Vi kan sedan köra `npm run compile` och den kommer att köra Sass och söka efter eventuella ändringar i källkodsfilerna. Så när vi sparar vår Sass fil, då kompileras det till css.

```bash
npm run compile
```

Vi är nu redo att börja designa sidan. De första stilarna styr förhoppningsvis upp lite grundläggande användbarhet, som vi kan bygga vidare på. Läs mer om den här exempelsidans stilar under [Design](design.md).

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

Funkar det? Bra.

#### Sass-variabler och stilar

{% hint style="info" %}
Detta är en uppgift med eget arbete.
{% endhint %}

Testa nu att skapa variabler för ett par färger på sidan. Passa även på att styla User-sidans lista också.

{% hint style="info" %}
Sidan [coolors.co](https://coolors.co/) har Sass export
{% endhint %}

* [ ] Välj 1-5 färger
* [ ] Lägg till dem med variabler i din Sass
* [ ] Styla User-sidans lista

 Fortsätt sedan här för att lära dig mer om [Sass](https://sass-lang.com/guide).

