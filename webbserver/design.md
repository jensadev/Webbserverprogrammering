---
description: Lite kort om exempel-projektets design.
---

# Design - inte så webbserver

## Idé

För projektets design så användes [**Figma**](https://www.figma.com/), skisserna finns [här](https://www.figma.com/file/tngmvFgOZ96E1xHm9Igr9o/Webbserver-node?node-id=0%3A1). Det kan underlätta att använda ett digitalt skiss-verktyg, men oftast är det enklast att börja med penna och papper. Design är svårt och en iterativ process som innehåller många ställningstaganden. Under processen så framträder ofta problem med designen, både estetiska och tekniska. Produkten förbättras på så vis stegvis.

### Färg

Design-processen för exempel-projektet började med valet av ett **färgschema**. Färgerna har känslan om något under vatten. 

![F&#xE4;rgschemat](../.gitbook/assets/figmacolors.png)

Användaren är på djupt vatten, ny kunskap, osäkerhet, men samtidigt lugna och sköna färger, trygghet. 

> Allt kommer att bli bra.

När ett färgschema har valts för en sida är det viktigt att testa **användbarheten**. [Wave](https://www.d.umn.edu/itss/training/online/wave/) och [Lighthouse](https://developers.google.com/web/tools/lighthouse) är exempel på verktyg för att testa användbarheten. Testerna för exempel-projektet visade att **kontrasten** inte var tillräcklig. Kontrasten skulle behöva ökas markant för att åtgärda problemet, något som resulterade i att sidan tappar undervattenskänslan. Det leder alltså till ett ställningstagande för design eller användbarhet. Jag gör här valet att behålla designen trots testresultatet. 

{% hint style="info" %}
Testa tidigt och ofta!
{% endhint %}

Rent praktiskt kan Sass-variabler underlätta arbetet med färger. Sass har även [moduler](https://sass-lang.com/documentation/modules/color) för att manipulera färg.

### Bild

Undervattenskänslan i färgschemat ledde sedan till bilden med bubblorna. Bilden skapades i **Adobe Illustrator** med **formatet** [**SVG**](https://developer.mozilla.org/en-US/docs/Web/SVG). Utöver det används ikoner från Material.io och NTI Gymnasiets logotyp, alla i SVG formatet.  

![Bubblor](../.gitbook/assets/bubbles-v2.svg)

![Ikon logo](../.gitbook/assets/nti_icon.svg)

### Text

Det praktiska arbetet med sidans titel, Webbserverprogrammering, ledde till ett intressant problem. Det visade sig att ett väldigt långt ord som inte kan avstavas av webbläsaren påverkar hur hela sidan ritas ut när  `meta viewport scale` används. Resultatet blev hemskt och behövde åtgärdas. Lösningen blev att lägga till ett bindestreck mellan Webbserver och programmering, Webbserver-programmering. Rent språkmässigt fel för svenskan, men den enklaste och bästa lösningen. En bra illustration av att med HTML och CSS så skapas komplexa layouter, vilket inte alltid fungerar som tänkt. Ibland är den tänkta designen inte genomförbar, därför är det viktigt att genom testning upptäcka problem.

Jag hoppas att dessa exempel från designprocessen kan hjälpa. De är val som behöver göras oavsett om en har skisser att utgå från eller inte. Men att utgå från skisser och design-idéer, ger ett stöd som alltid underlättar. Att planera vad du ska skapa och slipa på det är en del i en process. Processen fortsätter sedan under webbplatsens kodande. Att arbeta med processen i flera steg och att testa leder så gott som alltid till ett bättre slutresultat.

{% hint style="info" %}
Kom ihåg att sidan ska gå att använda, annars är det en meningslös produkt.
{% endhint %}

## Från HTML till webbplats

På det stora hela så handlar det om att stegvis arbeta utifrån existerande skisser med HTML och CSS. Tänk på att inte försöka designa HTML utan innehåll. Placeringen av element i HTML-kod är beroende av andra element, så innehållet kommer alltid påverka placeringen.

Var noga med att strukturera sidan korrekt och att HTML-elementen är korrekt stängda. Om elementen inte är stänga kan det bli väldigt svårt med stilarna. Det kan även leda till att stilar och annat inte visas med önskat resultat. Testa tidigt och ofta, [validera](https://validator.nu/) koden regelbundet.

Undvik även att positionera element absolut och tänk alltid på att ändra storleken på webbläsarfönstret, du kan inte förutsätta att användaren alltid kör samma upplösning som du.

Fastna inte heller i evigheter på detaljer innan du ens har någon design. Gå vidare, du kan alltid komma tillbaka.

{% hint style="info" %}
**Minimum Viable Product \(MVP\)**. Designa helheten så att det går att använda först. Gör sedan klart allteftersom.
{% endhint %}



