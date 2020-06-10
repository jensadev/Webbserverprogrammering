---
description: 'Att skapa en active page länk med Pugs mixin, block och variabler.'
---

# Nuvarande sida

{% hint style="info" %}
Samtliga ändringar för detta steg finns i följande [commit](https://github.com/jensnti/wsp1-node/commit/c7bcf747357e33fa564b2ebbfde5de738712d62f).
{% endhint %}

## Mixin

\*\*\*\*[**Mixins**](https://pugjs.org/language/mixins.html) är kodsnuttar i **Pug** som kan återanvändas, en sorts funktioner. För att ge exempel på hur mixins fungerar ska du skapa en active navigation länk till exempel-projektet. Sidans titel kommer att sparas som en variabel för att göra detta.

Mixinet för navigationen kommer att skapas i nav-vyn. I större projekt så kan det vara värt att strukturera upp mixins och filer som är uppbrutna i flera delar med mappar. Mixins kan såklart vara värda att spara och återanvända i andra projekt och då passar det utmärkt att spara i en separat fil.

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

#### users.pug

{% hint style="info" %}
Detta är en uppgift med eget arbete.
{% endhint %}

Gör nu samma ändringar för `users.pug` och gör klart navigationen i `nav.pug`.

* [ ] Skapa ett head block i `users.pug`
* [ ] Namnge sidan i titel variabeln
* [ ] Lägg till ytterligare länkar i `nav.pug` med det mixin vi skapat

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

