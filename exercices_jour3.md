# Vérification
```
docker compose 
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
mkdir prog_exo2
```
```
cd prog_exo2
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
# Exercices 3 : (docker compose network)
Ne changer pas de le chemin prog_exo2
```
mv docker-compose.yml docker-compose.yml.exo2
```
```

```

# Exercices 4 : (docker compose volumes)
Ne changer pas de le chemin prog_exo2
