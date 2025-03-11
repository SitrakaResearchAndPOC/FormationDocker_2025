# Exercices Jour 2
## Exercice 1 : NGINX
Suppression de la précédente version de Docker
Telechargeons une image nginx puis utilisons la redirection de port par l'option -p </br>
Testons le serveur </br>
```  
docker run -tid -p 8080:80 --name web nginx:latest
```  
Tapez dans le navigateur de la machine hôte l'addresse IP suivante : </br>
Tester sur le port 80 </br>
127.0.0.1:80 </br>
Tester sur le port 8080
127.0.0.1:8080 </br>
</br>
Pour avoir tous les propriétés de notre conteneur via docker inspect : 
```  
docker inspect web
```  
essayer de trouver l'addresse ip sous forme de 172.x.x.x  </br>
Tapez dans le navigateur de la machine hôte l'addresse IP suivante : </br>
Tester sur le port 80 </br>
127.0.0.1:80 </br>
Tester sur le port 8080
127.0.0.1:8080 </br>
</br>

## Exercice 2 : Persistance Volume
```  
docker ps
```  
Aucun conteneur qui demarre pour le moment </br>
Ajoute l'option -a pour tous les conteneurs presents dans la machine </br>
```  
docker ps -a
```  
Pour demarrer le conteneur web
```  
docker start web
```  
```  
docker ps
```  
verifie avec les navigateurs </br>
</br>
```  
docker exe -ti web sh
```  
Installer vim ou nano selon votre choix : 

```  
apt update
```  
```  
apt install vim nano
```  
Personnalisons le conteneur : 
```  
nano /usr/share/nginx/html/index.html
```  
changer "welcome nginx" avec "welcome docker sitraka"
Rafraichir la page </br>
Le problème c'est que près arrêt du conteneur via docker stop :
```  
exit
```  
```  
docker stop web
```  
```  
docker start web
```  
Rafraichir la page il y a pas de problème </br>
Imaginons qu'on perd notre conteneur par le commande 
``` 
docker rm -f web
```  
-f pour forcer la suppression même si c'est en cours d'execution
Recréeons le conteneur via docker run : 
```  
docker run -tid -p 8080:80 --name web nginx:latest
```  
```  
docker ps
```  
Par la suppression des conteneurs, le conteneur ne stocke pas la partie data </br>
</br>
Création de volume : 
```  
docker rm -f web
```  
Lors de la création, specifions -v pour volume
```  
docker run -tid -p 8080:80 -v /srv/data/nginx/:/usr/share/nginx/html --name web nginx:latest
```  
Rafraichir, nous avons un forbidden </br>
Car index.html n'existe pas </br>
Dans le repertoire local /srv/data/nginx </br>
Ajouter index.html </br>
Dans un nouveau terminal ctrl+shift+t (en linux)
``` 
cd /srv/data/nginx/
``` 
``` 
nano index.html
``` 
Ajouter dans le page
``` 
hello sitraka
``` 
Suppression de conteneur : 
``` 
docker rm -f
``` 
``` 
docker ps -a
``` 
```  
docker run -tid -p 8080:80 -v /srv/data/nginx/:/usr/share/nginx/html --name web nginx:latest
```  

## Exerice 3 : Persistance (docker volume)

#Créer un volume
```  
docker volume ls
```  
```  
docker volume create mynginx
```
```  
docker volume ls
```  
#Lancement de conteneur
```  
docker run -d --hostname myhost -v mynginx:/usr/share/nginx/html/ --name c1 nginx:latest
```
```  
docker exec -ti c1 bash
```
```  
cd /usr/share/nginx/html/ 
```
```  
ls
```
```  
cat index.html
```
```  
docker volume inspect mynginx
```  
Le point de montage : /var/lib/docker/volumes/mynginx/_data
```  
 sudo ls  /var/lib/docker/volumes/mynginx/_data
```
```  
cat index.html
```  
Dans le conteneur : 
```  
docker exec -ti c1 bash
```
```  
echo toto > /usr/share/nginx/html/index.html
```
ou bien à l'interieur : </br>
Créons un conteneur sur le même volume </br>
Supprimer des volumes
```
docker volume rm mynginx
```
```
docker run -ti --name c2 --rm -v mynginx:/data debian:latest bash
```
```
ls /data
```
on voit index.html
```
cat index.html
```
```
echo titi > index.html
```
revoie dans le contenteur c1, c'est changé automatiquement</br>
Suppression de volume
```
docker volume rm mynginx
```
Erreur (en cours d'utilisation) </br>
Persistance des volumes : 
```
docker rm -f c1
```
```
docker run -d --hostname -v mynginx:/usr/share/nginx/html/ --name c3 nginx:latest
```
```
cd /usr/share/nginx/html/
```
```
cat index.html
```
c'est encore titi
```
docker rm -f c3
```
```
docker volume rm mynginx
```
Question : Pourquoi on n'a pas supprimé le conteneur c2 ????
```
docker volume ls
```


## Exerice 4 : Débuter docker volume
```
docker volume
```
docker volume soit la création, soit l'inspection, ls, prune, rm  </br>
Création de volume : 
```
docker volume create monvolume
```
verification :
```
docker volume ls
```
```
docker volume inspect monvolume
```
Vérifier ou se trouve le mountpoint : /var/lib/docker/volumes/monvolume/_data </br>
Pour utiliser volume, il faut l'option --mount
```
docker run -tid --name web -p 8080:80 --mount source=monvolume,target=usr/share/nginx/html
```
regarder le point de montage
```
docker volume inspect monvolume
```
Le point de montage est : 
```
cd  /var/lib/docker/volumes/monvolume/_data
```
```
nano index.html
```
Rafraîchir la page </br>
Essaye de supprimer le volume 
```
docker volume rm monvolume
```
Message d'erreur ? car l'id est pris!!!
```
docker rm -f web
```
```
docker volume rm monvolume
```

## Exerice 5 : dockers volumes (approfondies)
Bind mount sur linux : 
```
mkdir /data
```
```
mkdir /data2
```
```
touch /data/toto
```
```
findmnt 
```
```
mount --bind /data/ /data2/
```
```
findmnt
```
Démonter
```
ls /data2
```
```
unmont /data2
```
```
rm -rf /data2
```
3 Types de montages de docker : 
* bind mount (regarder l'exterieur pour alimenter l'image)
* volume docker (Prendre l'image pour alimenter l'exterieur)
* tmpfs
```
mkdir /data
```
```
touch /data/toto
```
l'exterieur du conteneur alimente les données avec bind mount 
```
docker run -d --name c1 --mount type=bind,source=/data/,destination=/usr/share/nginx/html/ nginx:latest
```
```
docker exec -ti c1 bash
```
```
ls /usr/share/nginx/html
```
```
exit
```
```
docker inspect c1
```
regarder la liste de mount? </br>
ASTUCES POUR VOIR LES MONTAGES SEULEMENT :
```
docker inspect --format "{{.Mounts}}" c1
```
l'interieur alimente les données avec volume
```
docker volume create mynginx
```
```
docker run -d --name c2 --mount type=volume,src=mynginx,destination=/usr/share/nginx/html nginx:latest
```
```
docker exec -ti c2 bash
```
```
cat /usr/share/nginx/html/index.html
```
La source alimente
```
exit
```
tmpfs : sans persistance
```
docker run -d --name c3 --mount  type=tmpfs,destination=/usr/share/nginx/html nginx:latest
```
```
docker exec -ti c3 bash
```
```
echo toto > /usr/share/nginx/html/index.html
```
```
cat /usr/share/nginx/html/index.html
```
```
docker rm -f c3
```
```
docker run -d --name c3 --mount  type=tmpfs,destination=/usr/share/nginx/html nginx:latest
```
```
docker exec -ti c3 bash
```
Pas de persistance
```
ls /usr/share/nginx/html/index.html
```

## Exerice 6 : dockers volumes (approfondies)
SEQUENCE D'INSTRUCTION :  </br>
RUN : LANCEMENT DES COMMANDES APT ...  </br>
ENV : VARIABLE D'ENVIRONNEMENT  </br>
EXPOSE : EXPOSITION DES PORTS </br>
VOLUME : DEFINITION DES VOLUMES </br>
COPY : CP ENTRE HOST ET CONTENEUR </br>
ENTRYPOINT : PROCESSUS MAÎTRE </br>
Création de Dockerfile
```
nano Dockerfile
```
```
FROM ubuntu:latest
MAINTAINER sitraka
RUN apt-get update \
&& apt-get install -y vim git nano \
&& apt-get clean \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 
```
N'OUBLIER PAS LE POINT FINAL
```
docker build -t monimage:v1.0 .
```
Petite astuce (les tailles)
```
docker history monimage:v1.0
```
```
docker run -tid --name test monimage:v1.0
```
```
docker exec -ti test bash
```
```
git
```
```
exit
```
```
docker rm -f test
```
Simplifier la suppression d'image par rmi
```
docker rmi monimage:v1.0
```

TP8 : layers en docker
deux types de couches 
+ lecture seule
+ lecture - ecriture

Les images peuvent partager des couches : 
```
nano Dockerfile
```
```
FROM ubuntu:latest
RUN apt-get update
RUN apt-get install -y --no-install-recommends vim
RUN apt-get install -y --no-install-recommends git
RUN apt-get install -y --no-install-recommends nano
```
il y aura des couches pour chaques 
```
docker build -t monimage:v1.1
```
```
docker history monimage:v1.1
```
OU avec filtre
```
docker history monimage:v1.1 --format "{{.ID}}\t{{.CreatedBy}}"
```
MOINS DE COUCHE POUR : 
```
nano Dockerfile
```
```
FROM ubuntu:latest
RUN apt-get update
RUN apt-get install -y --no-install-recommends vim git vim
```
```
docker build -t monimage:v1.2
```
La limite de couche est environ 127

En mode ecriture avec run : 
```
docker run -tid --name test monimage:v1.1
```
```
docker exec -ti test bash
```
```
touch toto
```
```
rm -rf srv/
```
Que se passe t-il si nous faisons :
```
docker diff test
```
A : Append </br>
D : Drop  </br>
```
docker commit -m "creatin de nouvelle image " test monimage:v1.11
```

