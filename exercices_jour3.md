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
nano docker-compose
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
reboot
```
```
docker ps
```
```
reboot
```
```
docker ps
```
```
docker service ls
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


