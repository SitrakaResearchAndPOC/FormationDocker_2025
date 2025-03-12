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
version: '3'

services:
  myfirstservice:
    image: alpine
    restart: always
    container_name: myalpine
    entrypoint: ps aux

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
Tester en accédant un terminal : 
```
version: '3.8'

services:
  myfirstservice:
    image: alpine
    container_name: myalpine
    entrypoint: sh -c "while true; do sleep 30; done"
```
```
docker-compose up -d
```
```
docker-compose exec myfirstservice sh
```


