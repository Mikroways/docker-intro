***
# Docker Compose
---
## ¿Qué es Docker Compose?

* Herramienta que permite levantar aplicaciones compuestas por múltiples
  contenedores.
* La arquitectura se define y configura en un archivo de texto
  ([YAML](http://yaml.org)).
  * Simple e intuitivo.
* Se vale de un comando para:
  * Iniciar, detener y reconstruir servicios.
  * Ver el estado de los servicios, los logs, etc.

---
## Versiones de Docker Compose

* Hay dos versiones diferentes, la 1 y la 2.
* No son compatibles entre sí.
* Pequeños cambios en el archivo de texto.
* Veremos la sintaxis de la versión 2.

---
## Docker Compose: ejemplo

* Instalación de Wordpress.
  * Vamos a crear un archivo llamado docker-compose.yml.
  * Definiremos allí la arquitectura de la aplicación.
  * Nos valdremos del comando docker-compose para levantar Wordpress e
    interactuar con los contenedores generados.

---
## Levantando un Wordpress

```bash
version: '2'

services:
  db:
    image: mysql:5.7
    volumes:
      - "dbdata:/var/lib/mysql"
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    links:
      - db
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_PASSWORD: wordpress

volumes:
  dbdata: {}
```

---
## Levantando un Wordpress

```bash
$ docker-compose up -d
  Creating network "wordpress_default" with the default driver
  Creating volume "wordpress_dbdata" with default driver
  Creating wordpress_db_1
  Creating wordpress_wordpress_1

$ docker-compose ps
        Name                       Command               State         Ports
------------------------------------------------------------------------------------
wordpress_db_1          docker-entrypoint.sh mysqld      Up     3306/tcp
wordpress_wordpress_1   /entrypoint.sh apache2-for ...   Up     0.0.0.0:8000->80/tcp
```

---
## Levantando un Wordpress

<img alt="" src="images/compose-wordpress.png" height="320px" />

```bash
$ docker-compose logs -f
  wordpress_1  | 127.0.0.1 - - [16/Nov/2016:17:56:39 +0000] "GET / HTTP/1.1" 302 384 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.98 Safari/537.36"
  wordpress_1  | 127.0.0.1 - - [16/Nov/2016:17:56:39 +0000] "GET /wp-admin/install.php HTTP/1.1" 200 3410 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.98 Safari/537.36"
  wordpress_1  | 127.0.0.1 - - [16/Nov/2016:17:56:41 +0000] "GET /favicon.ico HTTP/1.1" 200 228 "http://127.0.0.1:8000/wp-admin/install.php" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.98 Safari/537.36"
```

***

