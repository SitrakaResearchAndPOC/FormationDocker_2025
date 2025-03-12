# Exercices Jour 2
## Exercice 1 : NGINX
* Créer un serveur nginx avec une exposition de port
* Tester les ports de la partie host ou partie conteneur
  
Suppression de la précédente version de Docker  </br>
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
e 172.17.x.x:80 </br>
Tester sur le port 8080
e 172.17.x.x:8080 </br>
</br>
```  
curl 172.17.x.x:8080
```  
```  
curl 172.17.x.x:80
```  

## Exercice 2 : Persistance Volume
* Tester qu'après suppression de conteneur les données disparaissent
* Utiliser l'option -v chemin1:chemin2 pour faire du bind mount persistant
* Tester la persistance

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
docker exec -ti web sh
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
changer "welcome nginx" avec "welcome docker sitraka"  </br>
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
docker rm -f web
``` 
``` 
docker ps -a
``` 
```  
docker run -tid -p 8080:80 -v /srv/data/nginx/:/usr/share/nginx/html --name web nginx:latest
```  

Rafraichir le page web pour avoir une Erreur forbiden </br>
va dans host dans le dossier : /srv/data/nginx/ </br>
```  
cd /srv/data/nginx/
```  
ajouter du code html et fichier index.html </br>
Rafraichir le page web 

* Pour windows :

Lancer dockerdesktop 
```  
mkdir -p data
```  
```  
docker run -itd --name web3 -p 8005:80 -v "$(Convert-Path './data'):/usr/share/nginx/html" nginx
```  
```  
docker rm -f web
```  

## Exerice 3 : Persistance (docker volume)
* Créer un volume mynginx
* créer un conteneur c1 nginx comme volume mynginx
* Verifier la persistance
* créer un conteneur c2 comme volume mynginx
* changer les données dans index.html (verifier persistance)
* créer un conteneur c3 comme volume mynginx
* verifier persistance

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
docker rm -f c1
```  
```  
docker run -itd -p 8080:80 --hostname myhost -v mynginx:/usr/share/nginx/html/ --name c1 nginx:latest
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
echo "twitwi" > index.html
```
```  
cat index.html
```
RAFRAICHIR LA PAGE -> affichage twitwi

Sortir vers le host : 
```
exit
```
```  
docker volume inspect mynginx
```  
Le point de montage : /var/lib/docker/volumes/mynginx/_data
```  
 sudo ls  /var/lib/docker/volumes/mynginx/_data
```
```  
cat /var/lib/docker/volumes/mynginx/_data/index.html
```
```
echo "toto" > /var/lib/docker/volumes/mynginx/_data/index.html
```

Dans le conteneur : 
```  
docker exec -ti c1 bash
```
```  
cat /usr/share/nginx/html/index.html
```
RAFRAICHIR LA PAGE -> affichage toto
```
exit
```
Créons un autre conteneur DEBIAN pour voir l'interaction avec le même volume </br>

```
docker run -ti --name c2 --rm -v mynginx:/data debian:latest bash
```
```
ls /data
```
on voit index.html dans /data (ancien données de docker volumes)
```
cat /data/index.html
```
AFFICHAGE -> toto
``` 
echo titi > /data/index.html
```
revoie dans le contenteur c1, c'est changé automatiquement</br>
RAFRAICHIR -> titi
```
exit
```
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
docker run -d --hostname myhost -v mynginx:/usr/share/nginx/html/ --name c3 nginx:latest
```
```
docker exec -it c3 bash
```
```
cd /usr/share/nginx/html/
```
```
cat index.html
```
c'est encore titi
RAFRAICHIR -> titi
```
exit
```
```
docker rm -f c3
```
```
docker volume rm mynginx
```
Question : Pourquoi on n'a pas supprimé le conteneur c2 càd on n'a pas fait docker rm -rf c3 ????
```
docker volume ls
```


## Exerice 4 : Débuter docker volume
* Créer puis verifier un volume
* Associer un volume à une conteneur
* Suppression de volume
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
docker run -tid --name web -p 8080:80 --mount source=monvolume,target=/usr/share/nginx/html nginx
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
CHANGER PUIS RAFRAICHIR (ajouter un mot de votre choix dans html)  </br>
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

RQ : Rappel bind mount sur linux   </br>
Bind mount sur linux : 
```
rm -rf /data /data2
```
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
rm -rf /data
```
```
mkdir /data
```
```
touch /data/toto
```
BIND MOUNT :  c'est le hôte qui alimente
```
docker rm -f c1
```

```
docker run -itd --name c1 --mount type=bind,source=/data/,destination=/usr/share/nginx/html/ nginx:latest
```
```
docker exec -ti c1 bash
```
```
ls /usr/share/nginx/html
```
CONCLUSION : On voit toto, un donnée de l'exterieur
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
docker volume create mynginx2
```
```
docker rm -f c2
```
```
docker run -d --name c2 --mount type=volume,src=mynginx2,destination=/usr/share/nginx/html nginx:latest
```
```
docker exec -ti c2 bash
```
```
cat /usr/share/nginx/html/index.html
```
Conclusion : le conteneur alimente les données
```
exit
```
Testons avec un point de montage ayant des données 
```
docker rm -f c2
```
```
docker volume create nginx3
```
```
cd /var/lib/docker/volumes/nginx3/_data
```
```
touch sitraka
```
```
touch sitraka2
```
```
docker run -d --name c2 --mount type=volume,src=mynginx3,destination=/usr/share/nginx/html nginx:latest
```
```
docker exec -ti c2 bash
```
```
ls /usr/share/nginx/html/
```
Conclusion : les données de conteneurs alimente les données

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
exit
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
Pas de persistance : Pas de index.html
```
ls /usr/share/nginx/html/
```

## Exerice 6 : Dockerfile 
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

## Exerice 7 : Dockerfile(approfondies)
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
docker build -t monimage:v1.1 .
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
## Exerice 8 : Docker network (introduction)
* Tester interconnexion avec Doker0 bridge
* Tester les options -p (avec publication de port) -P(avec publication non précise de port) --expose(sans publication juste déclaration de port)
Réseaux avec docker est nommé driver </br>
Types : bridge, host, none , overlay, macvlan </br>
docker0 : 172.17.0.1 (configurable) 
```
apt update 
```
```
apt install net-tools
```
```
ifconfig
```
OU 
```
ip a
```
```
docker network ls
```
capturer l'id de bridge </br>
docker inspect <id de bridge>  </br>
Retrouver 172.17.0.1 qui est l'adrresse IP par defaut de docker0
```
ifconfig docker0
```
```
docker rm -f c1
```
```
docker run --name c1 -d debian sleep infinity
```
```
docker ps
```
```
docker exec -ti c1 bash
```
```
apt update
```
```
apt install net-tools iputils-ping
```
```
ifconfig
```
à l'interieur de conteneur (hôte) pour tester que le docker0 est connecté à l'hôte :
```
ping 172.17.0.1
```
```
ping 8.8.8.8
```
```
exit
```

à l'exterieur de conteneur (hôte) pour tester que le docker0 est connecté à l'hôte :

```
ping 172.17.0.1
```
```
ping 8.8.8.8
```
```
docker rm -f c1
```
```
docker run -d --name c1 -p 8080:80 nginx
```
```
docker ps
```
```
ifconfig eth0
```
```
curl <ip addr>:8080
```
RAFRAICHIR en testant avec firefox 
```
docker rm -f c2
```
```
docker run -d --name c2 -P nginx
```
```
ifconfig eth0
```
```
docker ps
```
regarder le port concerné <port concerné>
```
curl <ip_addr>:<port concerné>
```
-P : exposition de port qui va être utilisé pour le publish all </br>
-p : publication de port </br>
```
docker run -d --name  c3 --expose 8080 nginx
```
```
docker ps 
```
declaration de port, virgule et non flêche
```
docker inspect c3
```
OU 
```
docker network ls
```
```
docker network inspect <id de bridge>
```
```
docker inspect c2
```
```
curl  <ip_addr c2>:80
```
```
docker inspect c3
```
```
curl  <ip_addr c3>:80
```
c'est juste une exposition (déclaration de port) et non une publication  </br>
Pas d'écoute

## Exerice 9 : Docker network (service dns)
* Verifier qu'une adresse IP n'est pas propre à un conteneur
* Utiliser un dns pour verifier la connexion entre conteneur

Les conteneurs n'ont pas des addresses ip statique  </br>
La destruction puis la recréation d'un conteneur ne donne pas une même addresse  </br>
```
docker rm -f c1
```
```
docker run -d --name c1 nginx:latest
```
```
docker inspect c1
```
```
docker stop c1
```
```
docker inspect c1
```
Pas d'addresse
```
docker inspect c1 | grep IPAddress
```
```
docker rm -f c2
```
```
docker run -d --name c2 nginx:latest
```
```
docker ps -a
```
```
docker inspect c2
```
c'est lui qui va prendre l'adresse IP
```
docker start c1
```
```
docker inspect c1
```
```
docker network ls
```
```
docker network create --driver=bridge --subnet=192.168.0.0/24 sitrakanet0
```
```
docker network ls
```
```
docker network inspect sitrakanet0
```
Créeons deux conteneurs :
```
docker rm -f c1
```
```
docker run -d --name c1 --network sitrakanet0 nginx:latest
```
```
docker rm -f c2
```
```
docker run -d --name c2 --network sitrakanet0 nginx:latest
```
```
docker ps
```
```
docker exec -ti c2 bash
```
```
apt update 
```
```
apt install iputils-ping
```
```
ping c1
```
```
exit
```
reessayez avec docker0
```
docker rm -f c1 c2
```
```
docker run -d --name c1  nginx:latest
```
```
docker run -d --name c2  nginx:latest
```
```
docker exec -ti c2 bash
```
```
apt update 
```
```
apt install iputils-ping  net-tools
```
```
ping c1
```
c'est normal qu'il n'y a pas interconnexion (pas de dns car default bridge)
```
ifconfig
```
```
exit
```
avant c'est utilisation link, aujourd'hui c'est docker connect
```
docker network connect sitrakanet0 c1
```
```
docker network connect sitrakanet0 c2
```
```
docker inspect c1
```
verifie l'addresse de c1
```
docker inspect c2
```
verifie l'addresse de c2
```
docker exec -ti c2 bash
```
```
ifconfig
```
```
ping c1
```
```
exit
```
Les drivers de types host :
```
docker rm -f c3
```
```
docker run -d --name c3 --network host nginx:latest
```
```
docker ps
```
```
curl 127.0.0.1
```
le nginx reponds sans mapping???
```
docker inspect c3
```
Pas d'addresse 
```
docker exec -ti c3 bash
```
```
apt update
```
```
apt install  net-tools
```
A l'interieur
```
ifconfig
```

Sortir vers l'exterieur :
```
exit
```
```
ifconfig
```
Conclusion : les configurations interieures et exterieures sont identiques en mode host
avec le mode host : </br>
Pas d'isolation réseau </br>
pas d'ip de conteneur </br>
ports uniques </br>

## Exerice 9 : Docker network (approfondie)
* source : 
[docker_network](https://www.youtube.com/watch?v=bKFMS5C4CG0)
* bridge par defaut : 
[dockernetwork_defaultbridge](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_defaultbridge.PNG)

```
ip address show
```
```
docker network ls
```
networktype mean driver </br>

[dockernetwork_defaultbridge_allmachine](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_defaultbridge_allmachine.PNG)

```
docker run -itd --rm --name thor busybox
```
```
docker run -itd --rm --name mjolnir busybox
```
```
docker run -itd --rm --name stormbreaker nginx
```
```
docker ps
```
connection par docker0 par defaut
```
ip address show
```
```
bridge link
```
```
docker inspect bridge
```
```
docker exec -it thor sh
```
```
ip addr
```
```
ping other_ip_addrr
```
```
ping 8.8.8.8
```
```
ip route  
```
(avec nat)
Exposition de port 80 dans stormbreaker : 
```
docker stop stormbreaker
```
```
docker run -itd --rm -p 80:8080 --name stormbreaker nginx
```
Dans le navigateur tapez localhost:8080

* Bridge :

[dockernetwork_userdefined_bridge](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_userdefined_bridge.PNG)
```
docker network create asgard
```
```
docker network ls
```
```
docker run -itd --rm --network asgard --name loki busybox
```
```
docker run -itd --rm --network asgard --name odin busybox
```
```
ip address show
```
```
bridge link
```
```
sudo docker inspect asgard
```
```
docker0 et asgard sont isolés : 
```
```
docker exec -it thor sh
```
```
ping addr_loki ou addr_odin
```
```
sudo docker exec -it loki sh
```
```
ping odin 
```
```
exit
```

* HOST :

[dockernetwork_host](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_host.PNG)

</br> Redeploiement de stormbreaker en tant que host : (pas besoin d'exposition de port)

```
docker stop stormbreaker
```
```
docker run -itd --rm --network host --name stormbreaker nginx 
```
stormbreaker devient host </br>
Pour le deploiement en VPN par exemple  


* MAC VLAN (bridge mode):
  
[dockernetwork_macvlan](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_macvlan.png)

</br> thor et mjolnir en tant que MAC VLAN :

```
ifconfig eth0
```
```
docker stop thor mjolnir
```
```
docker network create -d macvlan \
--subnet 172.28.160.0/20 \
--gateway 172.28.160.1 \
-o parent=eth0 \
newasgard
```
```
docker network ls
```
```
docker run -itd --rm --network newasgard \
--ip 172.28.160.121 \
--name thor busybox
```
```
docker exec -it thor sh
```
```
ip address show
```

* En mode promiscité :

[dockernetwork_macvlan_promuscuous](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_macvlan_promiscuous.png)

```
sudo ip link set eth0 promisc on
```
Refaire le deploiement avec mjolnir : 
```
docker run -itd --rm --network newasgard \
--ip 172.28.160.122 \
--name mjolnir busybox
```
```
docker exec -it mjolnir sh
```
```
ping thor
```
* MAC VLAN (802.1q mode):

[dockernetwork_macvlan_802.1q](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_macvlan_802.1q.png)

</br>
thor .20 </br>
mjolnir .30 </br> 

```
docker stop thor mjolnir
```
```
docker network rm newasgard
```
```
docker network create -d macvlan \
--subnet 172.28.160.0/20 \
--gateway 172.28.160.1 \
-o parent=eth0.20 \
macvlan20
```
```
ip address show
```
```
docker network create -d macvlan \
--subnet 172.28.160.0/20 \
--gateway 172.28.160.1 \
-o parent=eth0.30 \
macvlan30
```
* IPVLAN (L2):

[dockernetwork_ipvlan_l2.png](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_ipvlan_l2.png)

```
docker network create -d ipvlan \
--subnet 172.28.160.0/20 \
--gateway 172.28.160.1 \
-o parent=eth0 \
newasgard
```
```
docker network inspect
```
```
docker run -itd --rm --network newasgard \
--ip 172.28.160.121 \
--name thor busybox
```
```
docker exec -it thor sh
```
```
ping 172.28.160.1
```
Faire dans Linux et regarde le MAC : 
```
ip address show
```
dans windows ; verifie l'adresse MAC de l'adress IP (identique) : 
```
arp -a
```
dans windows : 
```
ping 172.28.160.121
```
Resultats: (avec connexion)
* IPVLAN (L3) comme un router:

[dockernetwork_ipvlan_l3](https://github.com/SitrakaResearchAndPOC/FormationDocker_2025/blob/main/dockernetwork/dockernetwork_ipvlan_l3.png)

```
docker stop thor mjolnir
```
```
docker network rm newasgard
```
``` 
docker network create -d ipvlan \
--subnet 192.168.94.0/24 \
-o parent=eth0 -o ipvlan_mode=l3 \
--subnet 192.168.95.0/24 \
-o parent=eth0 -o ipvlan_mode=l3 \
newasgard
```
Il est possible de ne pas specifier une addresse pour mode L3 :
```
docker run -itd --rm --network newasgard \
--ip 192.168.94.7 --name thor busybox 
```
```
docker run -itd --rm --network newasgard \
--ip 192.168.94.8 --name mjolnir busybox 
```
```
docker run -itd --rm --network newasgard \
--ip 192.168.95.7 --name loki busybox 
```
```
docker run -itd --rm --network newasgard \
--ip 192.168.95.8 --name odin busybox 
```
```
docker inspect newasgard
```
```
docker exec -it thor sh
```
```
ping google.com
```
(reseau isolé donc pas d'interconnexion)
```
ip route
```
```
ping mjolnir
```
```
ping loki
```
En ajoutant la table de routage du routeur et peut sortir vers internet
