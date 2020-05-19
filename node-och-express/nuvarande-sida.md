---
description: 'Att skapa en active page länk med Pugs mixin, block och variabler.'
---

# Nuvarande sida

Samtliga ändringar för detta steg finns i följande [commit](https://github.com/jensnti/wsp1-node/commit/c7bcf747357e33fa564b2ebbfde5de738712d62f).

## Mixin

För att testa att skapa en mixin så ska vi göra så att vi har en active navigation länk på vår sida. Vi kommer att använda oss av sidans titel som en variabel för att hitta denna.

En mixin är en funktion, enkelt sagt. Pug låter dig med en mixin skapa kod som kan återanvändas och kallas på vid behov.

Vi kommer att skapa denna mixin i `views/nav.pug`, i ett större projekt så kan det vara värt att strukturera upp mixins och partial filer med mappar, men detta duger nu. Men något som detta kan vara värt att  spara i en mixins fil, så att du kan ta med den till andra projekt.

{% code title="views/nav.pug" %}
```text
mixin nav-link(href, name)
  if name === title
    a.nav-link.active(href=href)= name
  else
    a.nav-link(href=href)= name

```
{% endcode %}

Så till att börja med så behöver vi skriva denna mixin först i filen, innan annan kod. Vår mixin tar två argument, precis som en funktion kan göra.

* href, för den faktiska länken som ska användas
* name, för titeln som ska visas på länken

Vi använder även en variabel vid namn titel för att styra vilken html som ska skapas. Denna variabel kommer från den aktiva views fil som används. Så för att lägga till en titel behöver vi redigera i både `views/layout.pug` och den aktiva sidans fil.

För att använda ett mixin så skriver du.

```text
+mixinname(params)
// för hem länken
+nav-link('/', 'Hem)
```

## Block

Inuti head taggen på layout-sidan behöver vi göra följande tillägg. 

{% code title="views/layout.pug" %}
```text
block head
  title= Webbserverprogrammering
```
{% endcode %}

Vi skapar en variabel, `title` och ger den värdet Webbserverprogrammering. Det låter alla underliggande sidor komma åt head blocket och byta ut innehållet i det.

Vi kan då låta den aktiva sidan vi laddat byta ut värdet på title och på så vis göra den aktiv med vårt tidigare mixin. Gör följande ändring.

{% code title="views/index.pug" %}
```text
block head
  -var title = "Hem"
  title Webbserverprogrammering - #{title}
```
{% endcode %}

Resultatet blir nu att på index sidan så visas titeln Webbserverprogrammering - Hem, och vår navbar känner igen titeln Hem och lägger till css klassen `active` för detta element.

Gör nu samma ändring för Users sidan.

## Stilar

Slutligen så behöver vi skapa .active klassen i vår css. Med Sass så skriver vi följande kod.

{% code title="public/stylesheets/style.sass" %}
```text
  .active
    color: $active-color
    &:hover
      color: $active-color-hover
```
{% endcode %}

Notera hur vi gör ett indrag och lägger till `:hover` regeln med ett & tecken. Sass tolkar det och skapar klasserna `.active` och `.active:hover` från det.

