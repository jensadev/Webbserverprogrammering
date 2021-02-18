# Automatiserade tester

## Setup

Testning kommer att göras med npm. För det behöver vi installera några paket. De är alla dev dependencies.

{% tabs %}
{% tab title="Bash" %}
```bash
npm install mocha chai supertest --save-dev
```
{% endtab %}
{% endtabs %}

Efter installationen är vi redo att köra igång. Test kommer att ligga i en mapp som ska heta test i projektets root.

{% tabs %}
{% tab title="Bash" %}
```bash
mkdir test
```
{% endtab %}
{% endtabs %}

För att köra test så används npm och ett script kommando från package.json. Notera att -r parametern enbart krävs om dotenv används.

{% code title="package.json" %}
```javascript
"scripts": {
    ...
    "test": "mocha -r dotenv/config --timeout 10000 --exit"
```
{% endcode %}

## Ett test med GET

Den här stilen av test kallas för Behaviour Driven Development\(BDD\), alltså beteende driven utveckling. Med detta menas att vi beskriver något som koden ska utföra och vad vi förväntar oss ska ske då.

I detta första test så ska vi testa en node servers index route, vi förväntar oss att den svarar med HTTP status 200, alltså ok. Om servern gör detta så vet vi att den är igång och fungerar.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="test/indextest.js" %}
```javascript
// ladda in dependencies
const expect = require('chai').expect;
const app = require('../app');
const request = require('supertest')(app);

// testet börjar här
describe('index route', () => {
  describe('GET /', () => {
    // vad förväntar vi oss ska ske, it should return...
    it('should return OK status', () => {
      // utför requesten, kontrollera att den svarar 200 och avsluta sedan testet
      request.get('/')
        .expect(200)
        .end((err, res) => {
          if (err) throw err;
        });
    });
  });
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

Testet kontrollerar aldrig vad sidan faktiskt svarar oss, utan enbart att status koden är 200. För att faktiskt se vad som returneras så behöver vi använda andra metoder för att kontrollera svaret.

Vi utökar testet för att se att index sidan välkomnar användaren.

{% tabs %}
{% tab title="PUG" %}
{% code title="views/index.pug" %}
```javascript
extends layout

block content
  h1 Welcome user
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="test/indextest.js" %}
```javascript
// testet börjar här
describe('index route', () => {
  describe('GET /', () => {
    ...
  });
  it('should greet the user', (done) => {
    request.get('/')
      .end((err, res) => {
        if (err) throw err;
        expect(res.text).to.contain('Welcome')
        return done();
      });
  });
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

## POST test

Att kunna skicka data med tester är väldigt användbart, då det tar bort ett stort manuellt moment med att 

