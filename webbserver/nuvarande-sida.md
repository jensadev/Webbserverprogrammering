---
description: 'Att skapa en active page länk med Pugs mixin, block och variabler.'
---

# Aktiv sida

{% hint style="info" %}
Koden för det här avsnittet finner du i denna [commit](https://github.com/jensnti/wsp1-node/commit/c7bcf747357e33fa564b2ebbfde5de738712d62f).
{% endhint %}

## Mixin

\*\*\*\*[**Mixins**](https://pugjs.org/language/mixins.html) är kodsnuttar i **Pug** som kan återanvändas, en sorts funktioner. För att ge exempel på hur mixins fungerar kommer du att skapa en active navigation länk till exempel-projektet.

Mixin kan antingen skapas i en enskild fil eller i en pug-templat. I det här exemplet skapas detta mixin i nav-vyn. Skriv mixin koden överst i filen.

{% hint style="info" %}
Mixins kan sparas i separata filer så att du kan återanvända dem mellan olika projekt.
{% endhint %}

{% code title="views/nav.pug" %}
```text
mixin nav-link(href, name)
  if name === title
    a.nav-link.active(href=href)= name
  else
    a.nav-link(href=href)= name

```
{% endcode %}

Studera koden ovan. Nav-link är namnet på den mixin som skapas. Denna mixin tar två **parametrar** \(argument\). Detta är som en [**funktion**](https://sv.wikipedia.org/wiki/Funktion_%28programmering%29) i de flesta programmeringsspråk.

* href, för den faktiska länken som ska användas
* name, för titeln som ska visas på länken

I koden används title **variabeln** från layout-vyns head **block**. Variabeln sätts till olika värden beroende på vilken aktiv vy som visas. Detta behöver läggas till i layout-vyn samt den aktiva sidan.

För att använda ett mixin är koden.

```text
+mixinname(params)
// dvs. för hem länken
+nav-link('/', 'Hem)
```

## Block

I head-taggen på layout-vyn så lägger du till.

{% code title="views/layout.pug" %}
```text
block head
  title Webbserverprogrammering
```
{% endcode %}

Först skapas ett **block** med namnet head, i detta initieras en variabel med namnet title som tilldelas värdet Webbserverprogrammering.  Det låter alla sidor som ärver av layout-vyn byta ut innehållet i head blocket. På det sättet kan variabeln tilldelas ett nytt värde i vy filerna.

{% code title="views/index.pug" %}
```text
block head
  -var title = "Hem"
  title Webbserverprogrammering - #{title}
```
{% endcode %}

Resultatet blir nu att på index sidan så visas titeln Webbserverprogrammering - Hem, samt att sidans navigation kan kontrollera titel-variabelns värde och lägga till css-klassen active på nuvarande sida.

#### users.pug

{% hint style="info" %}
Detta är en uppgift med eget arbete.
{% endhint %}

Färdigställ navigationen och koden i users-vyn.

* [ ] Skapa ett head block i filen users.pug
* [ ] Använd en titel-variabel för att ge sidan en titel.
* [ ] Skapa en länk till en Om-sida i navigationen. 

## Stilar

Active-klassens syfte är att ge användaren visuell återkoppling kring vilken sida som är aktiv. Klassen behöver alltså skilja sig från övriga länkar. Skriv följande kod.

{% code title="public/stylesheets/style.sass" %}
```css
  .active
    color: $active-color
    &:hover
      color: $active-color-hover
```
{% endcode %}

Notera strukturen och indenteringen. För att skapa `:hover` regeln i dokumentet används &-tecknet samt indraget. Sass tolkar det och skapar klasserna `.active` och `.active:hover` utifrån detta.

