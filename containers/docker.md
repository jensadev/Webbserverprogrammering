# Docker

## Installation

{% hint style="info" %}
Detta kräver att du har WSL2 installerat. Du kan kontrollera din version med Powershell, kör `wsl -l -v`
{% endhint %}

* Installera Windows Subsystem Linux 2, en guide finns [här](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
  * Om du installerar en ny distribution:
    * `sudo apt update`
    * `sudo apt upgrade -y`
* Installera [Docker desktop](https://www.docker.com/) för Windows.
  * Kör igenom Dockers Get started Tutorial i programmet.
* Se till att du har Remote extension för Visual Studio Code, den [här](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl).

Nästa steg är att konfigurera Docker så att det använder WSL2, se fig 1 och att det har tillgång till WSL, fig 2.

![Fig 1, General settings f&#xF6;r Docker](../.gitbook/assets/docker_use1.png)

![Fig 2, Resource settings. V&#xE4;lj den distribution du anv&#xE4;nder.](../.gitbook/assets/docker_use.png)

Du kan eventuellt behöva starta om datorn, WSL och eller Docker efter detta. Men du ska kunna starta WSL och köra kommandot docker. Docker körs då från Windows, men finns tillgängligt från WSL.

{% tabs %}
{% tab title="Bash" %}
```bash
docker
```
{% endtab %}
{% endtabs %}

## Ett första test med Node

Innan du kommer igång så krävs det att du installerar lite programvara lokalt.

```text
sudo apt install curl dirmngr apt-transport-https lsb-release ca-certificates -y
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt install nodejs -y
sudo npm i -g express express-generator
```

Nästa steg är att skapa ett Express-projekt som kommer utgöra basen för denna container.

{% tabs %}
{% tab title="Bash" %}
```bash
mkdir wsp1-docker-node
cd wsp1-docker-node
express --view pug --css sass --git
npm install
```
{% endtab %}
{% endtabs %}

För att underlätta utvecklingen används även nodemon.

{% tabs %}
{% tab title="Bash" %}
```bash
npm install --save-dev nodemon
```
{% endtab %}
{% endtabs %}

Starta sedan Visual Studio Code.

{% tabs %}
{% tab title="Bash" %}
```bash
code -r .
```
{% endtab %}
{% endtabs %}

Lägg sedan till följande kod så att nodemon körs.

{% code title="package.json" %}
```javascript
  "scripts": {
    "start": "nodemon ./bin/www"
  },
```
{% endcode %}

### Docker filer

Docker utgår från ett antal konfigurationsfiler för att skapa en image. Denna image är det som körs och används i en Docker container. Skapa följande filer i projektmappen.

{% code title="docker-compose.yml" %}
```yaml
version: "3"
services:
  node:
    container_name: nodejs
    image: "node:12"
    user: "node"
    working_dir: /var/www
    environment:
      - NODE_ENV=development
    volumes:
      - ./:/var/www
    ports:
    - "3000:3000"
    command: "npm start"
```
{% endcode %}

{% code title="Dockerfile" %}
```yaml
FROM node:12
WORKDIR /var/www
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```
{% endcode %}

Om du har skapat Express-projektet, Docker-filerna och allt fungerar så är du redo att skapa en image och testa den.

{% tabs %}
{% tab title="Bash" %}
```bash
docker-compose up -d
```
{% endtab %}
{% endtabs %}

Vänta på att imagen ska skapas, sedan öppnar du Docker-gränssnittet. Navigera till Containers och prova att öppna projektet i en webbläsare. Om allt fungerar så öppnas ditt Express projekt.

![Fig 3, Docker image running. ](../.gitbook/assets/docker_node.png)

Öppna sedan projektet med Visual Studio Code och prova att redigera projektets index filer. Ladda sedan om sidan i webbläsaren.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="routes/index.js" %}
```javascript
res.render('index', { title: 'Docker' });
```
{% endcode %}
{% endtab %}

{% tab title="PUG" %}
{% code title="views/index.pug" %}
```markup
extends layout

block content
  h1= title
  p Welcome to #{title}, this is amazing.

```
{% endcode %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Koden för hela exemplet finns [här](https://github.com/jensnti/wsp1-docker-node).
{% endhint %}

## Praktiskt

Filer för att skapa en image med node, npm och databas.

* Skapa ett nytt Express-projekt eller klona ett från Git.
* Skapa följande konfigurationsfiler i mappen.
* Se till att Docker är igång.
* Kör `docker-compose up -d build`
* Du bör nu kunna surfa till [localhost](http://localhost:3000)

{% tabs %}
{% tab title="Dockerfile" %}
{% code title="Dockerfile" %}
```yaml
FROM node:12

# Create app directory
WORKDIR /var/www

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm ci --only=production

# Bundle app source
COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```
{% endcode %}
{% endtab %}

{% tab title="docker-compose.yml" %}
{% code title="docker-compose.yml" %}
```yaml
version: "3"
services:
  node:
    container_name: node_dev
    image: "node:12"
    user: "node"
    working_dir: /var/www
    environment:
      - NODE_ENV=development
    volumes:
      - ./:/var/www
    ports:
    - ${port:-3000}:3000
    # expose:
    #   - "3000"
    command: "npm start"
    networks:
      - node-net

  #MySQL Service
  db:
    image: mysql:latest
    container_name: node_db
    restart: unless-stopped
    tty: true
    ports:
      - "3307:3307"
    environment:
      MYSQL_DATABASE: node
      MYSQL_ROOT_PASSWORD: SuperSecret123
      SERVICE_TAGS: dev
      SERVICE_NAME: mysql
    volumes:
      - dbdata:/var/lib/mysql/
      - ./mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - node-net

#Docker Networks
networks:
  node-net:
    driver: bridge
#Volumes
volumes:
  dbdata:

```
{% endcode %}
{% endtab %}

{% tab title=".dockerignore" %}
{% code title=".dockerignore" %}
```
node_modules
npm-debug.log
```
{% endcode %}
{% endtab %}

{% tab title="my.cnf" %}
{% code title="mysql/my.cnf" %}
```bash
[mysqld]
general_log = 1
general_log_file = /var/lib/mysql/general.log
secure-file-priv = ""

```
{% endcode %}
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="SQL" %}
```sql
CREATE USER 'nodeuser'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON nodeproject.* TO 'nodeuser'@'%';
ALTER USER 'nodeuser'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
FLUSH PRIVILEGES;
```
{% endtab %}
{% endtabs %}

