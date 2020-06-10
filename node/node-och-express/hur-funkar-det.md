# Hur funkar det?

Det här avsnittet handlar om hur **Express** fungerar. För att förstå det så kommer du att skriva om delar av startkoden som Express generator skapat. Du kommer att skapa en meny, redigera vyer och ändra routingen.

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

Det här projektets vy-struktur utgår från filen layout.pug. Underliggande sidor ärver **layout-vyns** struktur för att skapa en färdig HTML-sida. **Nyckelordet** \(på engelska **keyword**\) för detta är `extends`.

Filen index.pug ärver från layout.pug med

```text
extends layout
```

Layout-vyn är projektets HTML-bas så i den filen behövs en validerande HTML grund. Öppna filen och skriv följande kod.

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

**Index-vyn** är webbsidans startpunkt. Index-routen anropar index-vyn och skapar den HTML-kod som webbläsaren visar. Route filen kallar `res.render()` funktionen för att visa den vy som anges, i det här fallet index.

{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
```
{% endcode %}

I [render](https://expressjs.com/en/api.html#res.render) funktionen kallas först den vy som ska användas, index, sedan bifogas ett **objekt** till vy-filen. Objeketet innehåller här **egenskapen** title med Express som **värde**.

{% code title="views/index.pug" %}
```text
extends layout

block content
  main
    h1= title
    p Welcome to #{title}
```
{% endcode %}

I index-vyn kan sedan det bifogade objektet användas för att dynamiskt ändra vy-filen. Det kallas för template locals, läs mer om template locals [här](https://pugjs.org/language/interpolation.html).

### Nav

Med `extends` kan Pug återanvända kod. Det leder till enklare utveckling och mindre fel \([don't repeat yourself, DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)\). Ett bra användningsfall \(engelska [use case](https://en.wikipedia.org/wiki/Use_case)\) för detta är navigation på en webbsida. 

Du ska nu skapa en navigation. Redigera layout-vyn och skapa ett nav **block** efter body-taggen.

{% code title="views/layout.pug" %}
```text
body
  block nav
    include nav.pug
  block content
```
{% endcode %}

Nav-blocket följs av nyckelordet `include` som används för att lägga till innehållet från en annan fil, nav.pug. 

{% hint style="info" %}
Läs mer om [arv](https://pugjs.org/language/inheritance.html) och att [inkludera](https://pugjs.org/language/includes.html) filer i Pug.
{% endhint %}

Skapa sedan nav-vyn och lägg till HTML-koden.

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

Spara de redigerade filerna och ladda om sidan i webbläsaren.

### Users

Den users-route som Express generator skapat returnerar enbart en resurs. För att users-routen ska svara med en users-vy behöver `res.render` användas.

{% code title="routes/users.js" %}
```javascript
res.render('users', {});
```
{% endcode %}

Koden hänvisar nu till en users-vy som behöver skapas. Basera den på index-vyn.

{% code title="views/users.pug" %}
```text
extends layout

block content
  main
    h1= title
```
{% endcode %}

{% hint style="info" %}
Spara och ladda om, felsök vid behov.
{% endhint %}

För att visa Pugs och Express samspel mellan route och vy ska users-vyn uppdateras för att visa ett flertal användare. Skapa en [array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) i det objekt som bifogas av users-routen.

{% code title="routes/users.js" %}
```javascript
res.render('users', {'users': ['Hans', 'Moa', 'Bengt', 'Frans', 'Lisa'] });
```
{% endcode %}

Arrayen från users-routen kan nu användas som en template-local i users-vyn. För att visa innehållet i arrayen kommer en loop att användas. Formen för denna iteration är för varje user i users utför... Uppdatera users-vyn med följande kod.

{% code title="views/users.pug" %}
```text
ul
  each user in users
    li= user
```
{% endcode %}

{% hint style="info" %}
[Läs mer om iteration i Pug.](https://pugjs.org/language/iteration.html)
{% endhint %}

Först skapas ett ul-element följt av koden för loopen. I loopen skapas sedan ett li-element för varje index i users-arrayen. Ladda om sidan och se resultatet.

### Footer

{% hint style="info" %}
Detta är en uppgift med eget arbete.
{% endhint %}

I den här uppgiften ska du skapa en footer-vy som ska inkluderas på varje sida, förfarandet är mer eller mindre detsamma som för navigationen. Skapa vy-filen views/footer.pug och inkludera den från views/layout.pug. I filen skapar du ett footer element.

* [ ] Skapa footer.pug fil
* [ ] Skapa element och HTML-innehåll
* [ ] Inkludera footer-vyn i layout.pug

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

