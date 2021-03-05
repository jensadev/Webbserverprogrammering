# Hur fungerar det?

Det här avsnittet handlar om hur **Express** fungerar. För att förstå det så kommer du att skriva om delar av startkoden som Express generator skapat. Du kommer att skapa en meny, redigera vyer och ändra routingen.

## Pug

Express stöder ett flertal olika **templatmotorer** \(på engelska **template engine**\), den som används här är  [Pug](https://pugjs.org/). Pug är snarlikt html och relativt enkelt att komma igång med.

Innehållet på en sida med Pug struktureras med **indentering.** Text i början av en rad representerar generellt en **HTML** **tag**. Taggarna behöver inte stängas \(det sköter indenteringen\). För att **nästla** **element** indenteras de under andra taggar. Läs mer om [taggar](https://pugjs.org/language/tags.html) och [attribut](https://pugjs.org/language/attributes.html) i Pug.

Pug stöder **variabler**, **iteration** och **mixins**\(funktioner\) bland annat.

{% tabs %}
{% tab title="Pug" %}
```javascript
div#idname
  h1.classname Rubrik
  p.class1.class2 Brödtext
    | Fortsatt text i p elementet ovan.
  p Lorem...
    a(href='#') Länk i p texten
```
{% endtab %}
{% endtabs %}

Läs mer om Pug i [dokumentation](https://pugjs.org/)en och använd den vid behov. Det finns även ett antal **extensions** för [Visual Studio Code](https://code.visualstudio.com/) \(förkortat till **vscode**\) för att underlätta arbetet med Pug.

{% hint style="info" %}
Det går utmärkt att konvertera färdiga HTML-sidor till Pug, det finns flera verktyg för detta, [Google](https://www.google.com/search?q=html+to+pug&oq=html+to+pug&aqs=chrome..69i57j0l6j69i60.4848j0j7&sourceid=chrome&ie=UTF-8).
{% endhint %}

### Layout

Det här projektets vy-struktur utgår från filen layout.pug. Underliggande sidor ärver **layout-vyns** struktur för att skapa en färdig HTML-sida. **Nyckelordet** \(på engelska **keyword**\) för detta är `extends`.

Filen index.pug ärver från layout.pug med

{% tabs %}
{% tab title="Pug" %}
{% code title="views/index.pug" %}
```javascript
extends layout
```
{% endcode %}
{% endtab %}
{% endtabs %}

Layout-vyn är projektets HTML-bas så i den filen behövs en validerande HTML grund. Öppna filen och skriv följande kod.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/layout.pug" %}
```javascript
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
{% endtab %}
{% endtabs %}

### Index

**Index-vyn** är webbsidans startpunkt. Index-routen anropar index-vyn och skapar den HTML-kod som webbläsaren visar. Route filen kallar `res.render()` funktionen för att visa den vy som anges, i det här fallet index.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/index.js" %}
```javascript
/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

I [render](https://expressjs.com/en/api.html#res.render) funktionen kallas först den vy som ska användas, index, sedan bifogas ett **objekt** till vy-filen\(till pug\). Objeketet innehåller här **egenskapen** title med Express som **värde**.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/index.pug" %}
```javascript
extends layout

block content
  main
    h1= title
    p Welcome to #{title}
```
{% endcode %}
{% endtab %}
{% endtabs %}

I index-vyn kan sedan det bifogade objektet användas för att dynamiskt ändra vy-filen. Det kallas för template locals, läs mer om template locals [här](https://pugjs.org/language/interpolation.html). 

Byt ut title till något annat. Redigera sedan objektet och lägg till en annan egenskap. Visa sedan denna i index.pug.

### Nav

Med `extends` kan Pug återanvända kod. Det leder till enklare utveckling och mindre fel \([don't repeat yourself, DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)\). Ett bra användningsfall \(engelska [use case](https://en.wikipedia.org/wiki/Use_case)\) för detta är navigation på en webbsida. 

Du ska nu skapa en navigation. Redigera layout-vyn och skapa ett nav **block** efter body-taggen.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/layout.pug" %}
```javascript
body
  block nav
    include nav.pug
  block content
```
{% endcode %}
{% endtab %}
{% endtabs %}

Nav-blocket följs av nyckelordet `include` som används för att lägga till innehållet från en annan fil, nav.pug. 

{% hint style="info" %}
Läs mer om [arv](https://pugjs.org/language/inheritance.html) och att [inkludera](https://pugjs.org/language/includes.html) filer i Pug.
{% endhint %}

Skapa sedan nav-vyn och lägg till HTML-koden.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/nav.pug" %}
```javascript
nav
  ul
    li
      a(href='/') Home
    li
      a(href='/users') Users
```
{% endcode %}
{% endtab %}
{% endtabs %}

Spara de redigerade filerna och ladda om sidan i webbläsaren.

### Users

Den users-route som Express generator skapat returnerar enbart en resurs. För att users-routen ska svara med en users-vy behöver `res.render` användas.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/users.js" %}
```javascript
router.get('/', function(req, res, next) {
  res.render('users', {});
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

Koden hänvisar nu till en users-vy som behöver skapas. Basera den på index-vyn.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/users.pug" %}
```javascript
extends layout

block content
  main
    h1= title
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Spara och ladda om, felsök vid behov.
{% endhint %}

För att visa Pugs och Express samspel mellan route och vy ska users-vyn uppdateras för att visa ett flertal användare. Skapa en [array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) i det objekt som bifogas av users-routen.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/users.js" %}
```javascript
res.render('users', {'users': ['Hans', 'Moa', 'Bengt', 'Frans', 'Lisa'] });
```
{% endcode %}
{% endtab %}
{% endtabs %}

Arrayen från users-routen kan nu användas som en template-local i users-vyn. För att visa innehållet i arrayen kommer en loop att användas. Formen för denna iteration är för varje user i users utför... Uppdatera users-vyn med följande kod.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/users.pug" %}
```javascript
ul
  each user in users
    li= user
```
{% endcode %}
{% endtab %}
{% endtabs %}

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

\*\*\*\*[Sass ](https://sass-lang.com/)är en språk-utökning för att skapa **css.** Sass förkompileras till färdig css.

Express generator installerar ett middleware för att kompilera Sass-filer till css-filer. Projektets css skrivs i .sass filen vilken kompileras till .css kod när sidan hämtas från webbservern.

{% hint style="danger" %}
Sass-kod kan delas upp i flera filer, det är mycket praktiskt. För att inkludera filer används [`@use`](https://sass-lang.com/documentation/at-rules/use) regeln. Tyvärr har node-sass-middleware som experess använder ett äldre syntax. Därför används `@import` istället för `@use`.
{% endhint %}

### style.sass

Sass syntax skiljer något från css, men grunderna är liknande. Precis som Pug förlitar sig Sass på korrekt indentering för att fungera och strukturera. Öppna filen och skriv in följande.

{% tabs %}
{% tab title="CSS" %}
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
{% endtab %}
{% endtabs %}

De här stilarna ger en grundläggande formatering. Placeringen av elementen sker med [flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox). Prova att lägga till en font med [Google fonts](https://fonts.google.com/). Fonten behöver då länkas i layout-vyn, och Sass-stilen behöver uppdateras.

{% tabs %}
{% tab title="Pug" %}
{% code title="views/layout.pug" %}
```javascript
head
  link(href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" rel="stylesheet")
```
{% endcode %}
{% endtab %}
{% endtabs %}

För egenskaper som upprepas i Sass-kod är det praktiskt att skapa [variabler](https://sass-lang.com/documentation/variables). Det gör att du slipper upprepa kod och enkelt kan ändra värden.

{% code title="public/stylesheets/style.sass" %}
```css
$font: 'Roboto', sans-serif

h1
  font-family: $font
```
{% endcode %}

{% hint style="info" %}
[För att dela upp sass i flera filer se Git.](https://github.com/jensnti/wsp1-node/tree/master/public/stylesheets)
{% endhint %}

#### Arbeta med Sass

{% hint style="info" %}
Detta är en uppgift med eget arbete.
{% endhint %}

Testa nu att skapa variabler för ett par färger på sidan. Passa även på att styla User-vyns lista också.

{% hint style="info" %}
Sidan [coolors.co](https://coolors.co/) har Sass export.
{% endhint %}

* [ ] Välj 1-5 färger och skapa varaibler för dem
* [ ] Styla Users-vyn

 Läs mer om [Sass](https://sass-lang.com/guide).

