---
description: Allt i en Docker container
---

# Laravel, Nginx, Mysql

## Docker konfiguration

{% hint style="info" %}
Vill du läsa hela denna guide på engelska utan redigering så finns den [här](https://www.digitalocean.com/community/tutorials/how-to-set-up-laravel-nginx-and-mysql-with-docker-compose).
{% endhint %}

{% tabs %}
{% tab title="Bash" %}
```bash
git clone https://github.com/laravel/laravel.git tweety
```
{% endtab %}
{% endtabs %}

För att inte behöva installera Composer och dependencies lokalt.

{% tabs %}
{% tab title="Bash" %}
```bash
docker run --rm -v $(pwd):/app composer install
```
{% endtab %}
{% endtabs %}

Skapa följande mappar och filer.

{% tabs %}
{% tab title="Bash" %}
```bash
mkdir mysql
touch mysql/my.cnf
mkdir nginx
cd nginx
mkdir conf.d
cd conf.d
touch app.conf
cd ../../
mkdir php
touch php/local.ini
cd ..
touch docker-compose.yml
touch Dockerfile
```
{% endtab %}
{% endtabs %}

Starta Visual Studio Code och redigare filerna.

{% code title="nginx/conf.d/app.conf" %}
```text
server {
    listen 80;
    index index.php index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /var/www/public;
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```
{% endcode %}

{% code title="php/local.ini" %}
```text
upload_max_filesize=40M
post_max_size=40M
```
{% endcode %}

Viktigt med `secure-file-priv = ""` annars fungerar det inte.

{% code title="mysql/my.cnf" %}
```text
[mysqld]
general_log = 1
general_log_file = /var/lib/mysql/general.log
secure-file-priv = ""
```
{% endcode %}

Konfigurationsfiler för denna Docker image.

{% code title="docker-compose.yml" %}
```yaml
version: '3'
services:

  #PHP Service
  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: digitalocean.com/php
    container_name: app
    restart: unless-stopped
    tty: true
    environment:
      SERVICE_NAME: app
      SERVICE_TAGS: dev
    working_dir: /var/www
    volumes:
      - ./:/var/www
      - ./php/local.ini:/usr/local/etc/php/conf.d/local.ini
    networks:
      - app-network

  #Nginx Service
  webserver:
    image: nginx:alpine
    container_name: webserver
    restart: unless-stopped
    tty: true
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./:/var/www
      - ./nginx/conf.d/:/etc/nginx/conf.d/
    networks:
      - app-network

  #MySQL Service
  db:
    image: mysql:8.0.22
    container_name: db
    restart: unless-stopped
    tty: true
    command:
      '--character-set-server=utf8mb4'
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_ROOT_PASSWORD: Secret123
      SERVICE_TAGS: dev
      SERVICE_NAME: mysql
    volumes:
      - dbdata:/var/lib/mysql/
      - ./mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - app-network

#Docker Networks
networks:
  app-network:
    driver: bridge
#Volumes
volumes:
  dbdata:
    driver: local
```
{% endcode %}

{% code title="Dockerfile" %}
```yaml
FROM php:7.4-fpm

# Copy composer.lock and composer.json
COPY composer.lock composer.json /var/www/

# Set working directory
WORKDIR /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libonig-dev \
    libzip-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl
RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/
RUN docker-php-ext-install gd

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]
```
{% endcode %}

### Laravel

Laravel kräver en .env fil för att kunna köras.

{% tabs %}
{% tab title="Bash" %}
```bash
cp .env.example .env
```
{% endtab %}
{% endtabs %}

Redigera databasinformationen.

{% code title=".env" %}
```yaml
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laraveluser
DB_PASSWORD=your_laravel_db_password
```
{% endcode %}

### Kör

Du bör nu vara redo att bygga och starta denna image, det kan ta en stund.

{% tabs %}
{% tab title="Bash" %}
```bash
docker-compose up -d
```
{% endtab %}
{% endtabs %}

För att se alla containers som körs. Prova sedan att öppna den i webbläsaren, [localhos](http://localhost/)t.

{% tabs %}
{% tab title="Bash" %}
```bash
docker ps

CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                                      NAMES
57368c541885        nginx:alpine           "/docker-entrypoint.…"   10 seconds ago      Up 8 seconds        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   webserver
f09495147f49        mysql:8.0.22           "docker-entrypoint.s…"   10 seconds ago      Up 8 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp          db
3a0b42d5fb77        digitalocean.com/php   "docker-php-entrypoi…"   10 seconds ago      Up 8 seconds        9000/tcp                                   app
```
{% endtab %}
{% endtabs %}

Du behöver nu skapa en app:key, du kan eventuellt göra det direkt i webbläsare, annars kör du följande kommando för exekvera php artisan för att skapa en key.

{% tabs %}
{% tab title="Bash" %}
```bash
docker-compose exec app php artisan key:generate
```
{% endtab %}
{% endtabs %}

Den grundläggande konfigurationen är nu skapad.

### MySQL

För att slutföra konfigurationen av MySQL och skapa en användare så kopplar vi upp oss mot Docker imagen. Det ger dig ett bash shell direkt på imagen.

{% tabs %}
{% tab title="Bash" %}
```bash
docker-compose exec db bash
```
{% endtab %}
{% endtabs %}

Starta sedan mysql och skapa en user.

{% tabs %}
{% tab title="Bash" %}
```bash
mysql -u root -p
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="SQL" %}
```sql
CREATE USER 'laraveluser'@'%' IDENTIFIED BY 'Secret123';
GRANT ALL PRIVILEGES ON laravel.* TO 'laraveluser'@'%';
FLUSH PRIVILEGES;
```
{% endtab %}
{% endtabs %}

Avsluta sedan MySQL och Bash instansen på docker imagen med `exit`.

### Laravel migrate

Nu bör du kunna migrera Laravels databas-tabeller.

{% hint style="info" %}
Om du får fel, prova att rensa artisan config.
{% endhint %}

{% tabs %}
{% tab title="Bash" %}
```bash
docker-compose exec app php artisan migrate
```
{% endtab %}
{% endtabs %}

Testa att det fungerar på imagen med tinker.

{% tabs %}
{% tab title="Bash" %}
```bash
docker-compose exec app php artisan tinker
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Tinker" %}
```php
>>>\DB::table('migrations')->get();
=> Illuminate\Support\Collection {#3238
     all: [
       {#3236
         +"id": 1,
         +"migration": "2014_10_12_000000_create_users_table",
         +"batch": 1,
       },
       {#3245
         +"id": 2,
         +"migration": "2014_10_12_100000_create_password_resets_table",
         +"batch": 1,
       },
       {#3246
         +"id": 3,
         +"migration": "2019_08_19_000000_create_failed_jobs_table",
         +"batch": 1,
       },
     ],
   }
```
{% endtab %}
{% endtabs %}

### Cache

{% hint style="danger" %}
Att cacha en applikation låser konfigurationen. Om du får fel så kan du behöva uppdatera den.
{% endhint %}

 Du kan köra följande kommando för att skapa en cache för den, vilket snabbar upp din applikation.

```yaml
docker-compose exec app php artisan config:cache
```

