
```
docker-compose 
```
```
docker-compose
```
si non existant : 
```
apt update
```
```
apt install docker-compose
```
LE BUT SURTOUT C'EST PAS DE COPIE COLLER LE CODE DE DOCKER-COMPOSE MAIS L'ECRIRE VOUS MEME

# Exercices 1 : 
```
mkdir prog1
```
```
cd prog1
```
```
nano docker-compose.yml
```
```
version: '3'

services:
  myfirstservice:
    image: alpine
    restart: always
    container_name: myalpine
    entrypoint: ps aux
```
Tapez ctrl+x puis yes et entrée
```
docker-compose up -d
```
```
docker images
```
```
docker ps
```
```
docker ps -a
```
```
reboot
```
```
docker ps
```
```
docker-compose ps
```
```
docker-compose exec myfirstservice ps
```
```
reboot
```
```
docker ps
```
```
docker-compose down
```
```
cd ..
```
```
mkdir prog2
```
```
cd prog2
```

Tester en accédant un terminal : 
```
nano docker-compose.yml
```
```
version: '3.8'

services:
  myfirstservice:
    image: alpine
    container_name: myalpine
    entrypoint: sh -c "while true; do sleep 30; done"
```
Tapez ctrl+x puis yes et entrée
```
docker-compose up -d
```
```
docker-compose exec myfirstservice sh
```
```
exit
```
# Exercices 2 :
```
mkdir redis_flask
```
```
cd redis_flask
```
* docker-compose.yml
```
nano docker-compose.yml
```
```
version: '2'
services:
  app:
    build: .
    image: flask-redis:1.0
    environment:
      - FLASK_ENV=development
    ports:
      - 80:80
  redis:
    image: redis:4.0.11-alpine
```
Tapez ctrl+x puis yes puis entrée

* app.py

```
nano app.py
```
```
from flask import Flask, request, jsonify
from redis import Redis

app = Flask(__name__)
redis = Redis(host="redis", db=0, socket_timeout=5, charset="utf-8", decode_responses=True)

@app.route('/', methods=['POST', 'GET'])
def index():

    if request.method == 'POST':
        name = request.json['name']
        redis.rpush('students', name)
        return jsonify({'name': name})

    if request.method == 'GET':
        return jsonify(redis.lrange('students', 0, -1))
```
* requirements.txt

Tapez ctrl+x puis yes puis entrée
```
nano requirements.txt
```
```
flask
redis
```
Tapez ctrl+x puis yes puis entrée
* Dockerfile

```
nano Dockerfile
```
```
FROM python:3.7.0-alpine3.8
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV FLASK_APP=app.py
CMD flask run --host=0.0.0.0 --port 80
```
Tapez ctrl+x puis yes puis entrée

* post-get.sh
```
nano post-get.sh
```
```
#!/bin/bash

echo "Contenu de la base redis avant POST"
curl localhost:80

echo "POST..."
curl --header "Content-Type: application/json" --request POST --data '{"name":"xavki blog"}' localhost:80

echo "Contenu de la base redis après POST"
curl localhost:80
```
Tapez ctrl+x puis yes puis entrée
* Vérification
```
ls
```
```
cat docker-compose.yml
```
```
cat app.py
```
```
cat requirements.txt
```
```
cat post-get.sh
```

* Construction de l'image
```
docker-compose up -d
```

```
docker-compose ps
```
```
docker images
```
```
docker-compose stop
```
```
docker ps
```
```
docker ps -a
```
```
docker-compose ps 
```
```
docker-compose ps -a
```
```
docker-compose start
```
```
docker ps
```
```
docker ps -a
```
```
docker-compose ps 
```
```
docker-compose ps -a
```
```
chmod 777 post-get.sh
```
```
./post-get.sh
```
```
./post-get.sh
```
```
docker-compose down
```
# Exercices 3 : (docker-compose network)
* ANALYSE DU RESEAU
```
docker-compose up -d
```
```
docker network ls
```
Inspection du reseau : </br>
```
docker inspect <conteneur de l’application> 
```
-- Regarder subnet et gateway </br>
-- Regarder les deux conteneurs </br>
-- Pas de déclaration de réseaux (fait automatiquement) </br>
-- L’application app.py : Juste host et non ip </br>
```
docker ps
```
```
docker-compose ps
```
* AJOUT DU RESEAU
Ne changer pas de le chemin redis_flask 
```
mv docker-compose.yml docker-compose.yml.redis_flask
```
```
nano docker-compose.yml
```
```
version: '3'
services:
  app:
    build: .
    image: flask-redis:1.0
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
    networks:
      - backend
      - frontend
  redis:
    image: redis:4.0.11-alpine
    # AJOUT DE NETWORKS
    networks:
      - backend
# DECLARATION DES RESAUX
# PAS DE TITRET CAR C'EST PAS UN TABLEAU
networks:
  backend:
  frontend:
```
Enregistrer en tapant ctrl+x puis yes puis entrée
* ANALYSE DU RESEAU
```
docker-compose up -d
```
```
docker network ls
```
Inspection du reseau : </br>
```
docker inspect <conteneur de l’application> 
```
-- Regarder subnet et gateway </br>
-- Regarder les deux conteneurs </br>
-- Pas de déclaration de réseaux (fait automatiquement) </br>
-- L’application app.py : Juste host et non ip </br>
```
docker ps
```
```
docker-compose ps
```
```
docker exec -ti <conteneur de l’application > sh
```
```
ping redis
```
```
apk update
```
```
apk add nmap
```
-- port ouvert 6379
```
nmap  -PM -p 6379 redis
```
-- port fermé 80
```
nmap  -PM –p 80 redis
```
--Tout port : 
```
nmap -PM redis
```
-- A l’exterieur de docker
```
nmap -PM -p 6379   <ip_addr_redis> 
```



# Exercices 4 : (docker-compose volumes)
Ne changer pas de le chemin redis_flask
```
mv docker-compose.yml docker-compose.yml.redis_flask.1
```
```
nano docker-compose.yml
```
```
version: '3'
services:
  app:
    build: .
    image: flask-redis:1.0
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
    networks:
      - backend
      - frontend
  redis:
    image: redis:4.0.11-alpine
    networks:
      - backend
    # AJOUT DE VOLUME POUR LE SERVEUR REDIS
    volumes:
      - dbdata:/data

networks:
  backend:
  frontend:
# DECLARATION DE VOLUME
volumes:
  dbdata:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: '/srv/redis'
```
Enregistrer en tapant ctrl+x puis yes puis entrée</br> </br>
```
rm -f /srv/redis/*
```
```
mkdir  -p /srv/redis
```
```
ls /srv/redis/
```
```
docker-compose up -d -build
```
```
docker volume ls
```
```
docker volume inspect <nom_volume>_dbdata
```
```
./post-get.sh
```
```
./post-get.sh
```
Effaçons tous les services : 
```
docker-compose down
```
```
docker-compose up –d
```
```
./post-get.sh
```
-- Vérifier les persistances


* VOLUME DANS LA PARTIE CLIENT-SERVEUR
Ne changer pas de le chemin redis_flask
```
mv docker-compose.yml docker-compose.yml.redis_flask.2
```
```
nano docker-compose.yml
```
```
version: '3'
services:
  app:
    build: .
    image: flask-redis:1.0
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
    networks:
      - backend
      - frontend
    # AJOUT D'UN VOLUME SUR LE CLIENT
    volumes:
      - dbdata:/database
  redis:
    image: redis:4.0.11-alpine
    networks:
      - backend
    volumes:
      - dbdata:/data

networks:
  backend:
  frontend:

# DECLARATION DE VOLUME
volumes:
  dbdata:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: '/srv/redis'
```
Enregistrer en tapant ctrl+x puis yes puis entrée </br></br>

```
docker-compose down
```
```
docker-compose up -d build
```
```
docker ps
```
```
docker exec -it <nom_conteneur>  sh
```
```
ls /database
```
-- Existence de dump.rdb


# Exercices 5 :
* Préparation des chemins
```
mkdir web_php
```
```
cd web_php
```
* Code
```
nano index.php
```
```
<p> Bonjour </p>
```
Enregistrer en tapant ctrl+x puis yes puis entrée
* docker-compose.yml
``` 
nano docker-compose.yml
```
```
version: '3'

services:
  php:
    image: php:8.3-fpm
    volumes:
      - .:/var/www/html

  web:
    image: nginx:latest
    volumes:
      - .:/var/www/html
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "8888:80"
```
Enregistrer en tapant ctrl+x puis yes puis entrée
* nginx.conf
```
nano nginx.conf
```
```
events {}

http {

    server {

        listen 80;

        root /var/www/html;
        index index.php;

        location ~ \.php$ {

            fastcgi_pass php:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;

        }

    }

}
```
Enregistrer en tapant ctrl+x puis yes puis entrée
* Vérification des donnés
```
cat index.php
```
```
cat docker-compose.yml
```
```
cat nginx.conf
```
  
* création et lancement de conteneur
```
docker-compose up -d
```
```
docker-compose start
```
```
docker-compose ps
```
```
docker-compose stop
```

```
docker-compose down
```
Tester en tapant localhost:8888 ou curl localhost:8888  
```
curl localhost:8888
```
</br>
Ajouter des erreurs sur nginx puis lancer docker-compose </br>
Ajouter des erreurs sur le code puis lancer docker-compose </br>
Modifier la version de php en 7.4-fpm puis lancer docker-compose </br>

# Exercices 6 : web_php_mysql_nginx
* Préparation des chemins
```
mkdir web_php_mysql_nginx
```
```
cd web_php_mysql_nginx
```
```
mkdir code
```
* NGINX SEULEMENT
* docker-compose.yml
``` 
nano docker-compose.yml
```
```
version : '3'

services:
  nginx:
    image: nginx:1.22-alpine
    ports:
      - "8080:80"
    volumes:
      - ./code:/code
```
Enregistrer en tapant ctrl+x puis yes puis entrée

* tester les dossiers et fichiers
```
ls
```
```
cat docker-compose.yml
```
* Création des services
```
docker-compose up -d
```
RAFRAICHIR -> localhost:8080
```
curl localhost:8080
```
```
docker ps
```
```
docker-compose down
```

* NGINGX + PHP
``` 
nano docker-compose.yml
```
```
version : '3'

services:
  nginx:
    image: nginx:1.22-alpine
    ports:
      - "8080:80"
    volumes:
      - ./code:/code
      # Ajouter dans volumes les configurations
      - ./nginx.conf:/etc/nginx/nginx.conf
 # service php aussi
  php:
    image: php:8.3-fpm-alpine
    volumes:
      - ./code:/code
```
* nginx.conf
```
nano nginx.conf
```
```
events{}

http {

    server {

        listen 80;
        server_name localhost;

        root /code;  # Définition du dossier racine du site
        index index.php;

        location ~ \.php$ {

            fastcgi_pass php:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;

        }

    }

}
```
Enregistrer en tapant ctrl+x puis yes puis entrée
* le code
```
nano code/index.php
```
```
<p> Bonjour sur mon site web </p>
```
Enregistrer en tapant ctrl+x puis yes puis entrée

* tester les dossiers et fichiers
```
ls
```
```
cat code/index.php
```
```
cat nginx.conf
```

```
cat docker-compose.yml
```

* Création des services
```
docker-compose down
```
```
docker-compose up -d
```
RAFRAICHIR -> localhost:8080
```
docker ps
```
```
curl  localhost:8080
```
* NGINX + PHP + MYSQL + PHPMYADMIN
```
nano docker-compose.yml
```
```
version : '3'

services:
  nginx:
    image: nginx:1.22-alpine
    ports:
      - "8080:80"
    volumes:
      - ./code:/code
      # Ajouter dans volumes les configurations
      - ./nginx.conf:/etc/nginx/nginx.conf
 # service php aussi
  php:
    image: php:8.3-fpm-alpine
    volumes:
      - ./code:/code
  #Ajouter services mysql et phpmyadmin
  mysql:
    image: mysql:8
    environment:
      # 🚨 Changer si vous utilisez cette configuration en production
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: appdb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - dbdata:/var/lib/mysql
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      PMA_HOST: mysql
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "8081:80"

volumes:
  dbdata:

```
Enregistrer en tapant ctrl+x puis yes puis entrée
```
docker-compose down
```
```
docker-compose up -d
```
RAFRAICHIR: localhost:8081 (via interface graphique pour phpmyadmin)

* CONNEXION DB
```
nano code/index.php
```
```
<?php

echo "<p>Bonjour !</p>";

$servername = "mysql";
$username = "user";
$password = "password";
$dbname = "appdb";

try {

    $connexion = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);

} catch (PDOException $e) {

    echo "Error: " . $e->getMessage();

}
```
Enregistrer en tapant ctrl+x puis yes puis entrée
```
cat code/index.php
```
RAFRAICHIR localhost:8080
```
curl localhost:8080
```
Apparition d'erreur sur le code connexion pdo Error: "could not find driverroot"
* RESOLUTION D'ERREUR DE CONNEXION : DOCKER FILE
```
nano docker-compose.yml
```
```
version : '3'

services:
  nginx:
    image: nginx:1.22-alpine
    ports:
      - "8080:80"
    volumes:
      - ./code:/code
      # Ajouter dans volumes les configurations
      - ./nginx.conf:/etc/nginx/nginx.conf
 # service php aussi
  php:
    # MODIFICATION : pas tag image --> docker file	
    # COMMENTER IMAGE REMPLACER PAR BUILD l'image car on va utiliser Dockerfile ayant FROM  php:8.3-fpm-alpine
    # image: php:8.3-fpm-alpine
    # Il faut utiliser build pour installer via Dockerfile 
    build : .
    volumes:
      - ./code:/code
  #Ajouter services mysql et phpmyadmin
  mysql:
    image: mysql:8
    environment:
      # 🚨 Changer si vous utilisez cette configuration en production
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: appdb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - dbdata:/var/lib/mysql
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      PMA_HOST: mysql
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "8081:80"

volumes:
  dbdata:
```
Enregistrer en tapant ctrl+x puis yes puis entrée
```
nano Dockerfile
```
```
FROM php:8.3-fpm-alpine
RUN docker-php-ext-install mysqli pdo pdo_mysql
```
Enregistrer en tapant ctrl+x puis yes puis entrée

* Vérification des donnés
```
cat docker-compose.yml
```
```
cat Dockerfile
```
* création et lancement de conteneur
```
docker-compose down
```
```
docker-compose up -d
```
RAFRAICHIR localhost:8080
```
curl localhost:8080
```
PAS D'ERREUR 
* DEVELOPPEMENT
```
nano code/index.php
```
```
<?php

echo "<p>Bonjour !</p>";

$servername = "mysql";
$username = "user";
$password = "password";
$dbname = "appdb";

try {

    $connexion = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
    
    $sql = "CREATE TABLE IF NOT EXISTS example_table (
        id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
        fistname VARCHAR(30) NOT NULL,
        lastname VARCHAR(30) NOT NULL
    )";

    $connexion->exec($sql);

    $sql = "INSERT INTO example_table(fistname, lastname)
            VALUE ('John', 'DAC')";

    $connexion->exec($sql);


} catch (PDOException $e) {

    echo "Error: " . $e->getMessage();

}
```
Enregistrer en tapant ctrl+x puis yes puis entrée
Rafraîchir via
```
curl localhsost:8080
```
RAFRAICHIR BD : localhost:8081
```
cd ..
```

# Exercice 7 : web_php_node_go
```
mkdir web_php_node_go
```
```
cd web_php_node_go
```
```
mkdir go_app
```
```
mkdir node_app
```
```
mkdir php_app
```
```
ls
```
* Ecriture des codes : 
```
nano go_app/main.go
```
```
package main

import (
	"fmt"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "Hello World from Go!")
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}
```
Enregistrer en tapant ctrl+x puis yes puis entrée
```
nano node_app/server.js
```
```
const http = require('http');

const server = http.createServer((req, res) => {
    if (req.url.startsWith('/node')) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World from Node.js!');
    } else {
        res.writeHead(404, { 'Content-Type': 'text/plain' });
        res.end('Not Found');
    }
});

server.listen(3000, () => {
    console.log('Node.js app running on port 3000');
});
```
Enregistrer en tapant ctrl+x puis yes puis entrée
```
nano php_app/index.php
```
```
<?php
echo "Hello World from PHP!";
?>
```
Enregistrer en tapant ctrl+x puis yes puis entrée
```
nano nginx.conf
```
```
events {}

http {
    server {
        listen 80;

        location /node {
            proxy_pass http://node_app:3000;
        }

        location /php {
            proxy_pass http://php_app:8000;
        }

        location /go {
            proxy_pass http://go_app:8080;
        }
    }
}
```
Enregistrer en tapant ctrl+x puis yes puis entrée
* verification des données
```
cat go_app/main.go
```
```
cat node_app/server.js
```
```
cat php_app/index.php
```
```
cat nginx.conf
```
EXERCICES AJOUTE docker-compose.yml
