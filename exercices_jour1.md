# Exercice Jour 1 
## Exercice 1 : Premiers pas et installation
Suppression de la précédente version de Docker
```  
sudo apt remove docker docker-engine docker.io containerd runc
```
Mise en place du dépôt Docker
```  
sudo apt install ca-certificates curl gnupg lsb-release
```
``` 
sudo mkdir -m 0755 -p /etc/apt/keyrings
```
```  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
```  
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```  
Installation de docker
```  
sudo apt update
```
```  
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Premier pas 
```  
docker ps
```
```  
docker run nginx:latest
```
```  
docker ps
```
```  
docker ps -a
```
```  
docker run -d nginx:latest
```  
```  
docker ps
```
```  
docker ps -a
```

# Exercice 2 : 
```
docker run --help
```
```
docker run -d --name c1 nginx:latest
```
```
docker ps 
```
```
docker stop c1
```
```
docker ps 
```
```
docker ps -a
```
```
docker start c1
```
```
docker ps 
```
-f car le conteneur est actif : 
```
docker rm -f c1
```
```
docker ps -a
```
Terminal interactif 
```
docker run -ti --name c1 nginx:latest
```
ça marche pas avec ls
```
ls
```
ctrl+c
```
docker rm -f c1
```
Le conteneur prevoit l'utilisation de terminal : 
```
docker run -ti debian:latest
```
```
apt update 
```
```
apt install git
```
```
hostname 
```
c'est un problème car c'est un id et non un nom : 
```         
exit
```
```
docker run -ti --name c1 debian:latest
```
```
docker ps -a 
```
Si je recrée un conteneur avec le même nom ca marche pas Erreur conflict deamon quand il y a deux c1
```
docker run -ti --name c1 debian:latest
```
Ajouter --rm : 
--rm : suppresion après arrêt du conteneur
```
docker rm -f c1
```
```
docker run -ti --rm --name c1 debian:latest
```
```
exit
```
```
docker ps -a
```
Avec hostname : 
```
docker run -ti  --rm --hostname host1 --name c1 debian:latest
```
```
hostname
```
```
exit
```
autres elements comme dns : (mode debug)  </br> 
docker run -ti  --rm --hostname host1 --name c1 --dns 8.8.8.8 debian:latest  </br> 
ASTUCES : 
```
docker ps -a
```
```
docker run -d --name c1 nginx
```
```
docker ps -a
```
```
docker ps
```
lister les id 
```
docker ps -q 
```
Imbrication de commande : 
```
docker rm -f $(docker ps -q)
```
```
docker rm -f $(docker ps -aq)
```
```
docker ps 
```
Publication de port : 
```
docker run -d --name c1 -p 8080:80  nginx:latest 
```
Entrer sur le navigateur : 
```
localhost:8080
```
# Exercices 3 : Variables d'environnements
Lister les images via docker image ls
```
docker image ls
```
Pour avoir un tty, il faut toujours le mode detaché! </br>
Pour le variable d'environnement, il est possible d'utiliser --env ou -e </br>
```
docker run -tid --name testenv --env MYVARIABLE="123456" ubuntu:latest
```
```
docker ps
```
Pour rendre dans le conteneur, l'option exec
```
docker exec -ti testenv sh
```
OU 
```
docker exec -ti testenv bash
```
utiliser la commande env
```
env
```
```
echo $MYVARIABLE
```
```
ps
```
En combinant les deux directement :
```
docker exec -ti testenv env
```
C'est facile, mais il y a certain variable qu'on veut pas faire passer par cli : 
```
nano vars_env.lst
```
```
MYPASSWORD="123456"
MYDB="vm01"
```
```
docker rm -f testenv
```
```
docker run -tid --name testenv --env-file vars_env.lst ubuntu:latest
```
```
docker exec -ti testenv bash
```
```
env
```
Un variable environnement particulier le hostname : 
```
exit
```
```
docker rm -f testenv
```
```
docker run -tid --name testenv --hostname sitraka ubuntu:latest
```
```
docker exec -ti testenv bash
```
```
  hostname
```

# Exercice 4 : 
Création d'une image en installant git, nano, vim

```
docker image ls
```
```
docker exec -ti test_env bash
```
```
apt update
```
```
apt install git vim nano 
```
Le problème avec l'image c'est qu'il faut savoir ce que vous avez dejà installé avec
```
git
```
```
exit
```
```
docker ps
```
capteur l'id de conteneur
```
docker commit -m  "création d'image comme commentaire" <id conteneur> monimage:v1.0
```
```
docker image ls
```
Demarrons maintenant l'image : 
```
docker run -tid --name conteneurUbuntuPers monimage:v1.0
```
```
docker ps
```
Connexion dans notre conteneur
```
docker exec -ti  conteneurUbuntuPers bash
```
```
git
```
```
exit
```
```
docker image ls
```
Captuer id image
```
docker history <id image> 
```
OU
```
docker history monimage:v1.0
```
Suppression d'image (petit problème en testant ça)
```
docker image rm <id image> 
```
Il faut supprimer le conteneur pour supprimer l'image : 
```
docker rm -f conteneurUbuntuPers
```
```
docker image rm <id image> 
```
OU
```
docker image rm monimage:v1.0
```
```
docker image ls
```

# Exercice 5 : Methode peu connue (sauvegarder et charger une image docker)
Sauvegarder 

docker save -o dir/name.file <registry><image>:version

Charger  :

docker load -i <fichier.tar>
```
docker pull alpine
```
```
docker save -o myalpine.tar alpine:latest
```
```
mkdir dtar
```
```
tar -C dtar/ -xvf myalpine.tar
```
```
cd dtar/
```
```
docker rmi alpine
```
```
docker load -i myalpine.tar
```
```
docker images
```

# Exercice 6 : les tags pour les versions 
docker tag  <images_source>:<version> <images_dest>:<version>
```
docker pull alpine
```
```
docker image ls
```
OU 
```
docker images 
```
```
docker tag alpine:latest myalpine:v1.0
```
```
docker images
```
Deux tags mais un seul image
```
docker run -ti myalpine:v1.0
```
Créer un tag la version v1.0 en latest
```
docker tag  myalpine:v1.0 myalpine
```
```
docker run -ti myalpine
```
Bonne pratique : Quand on monte en version
```
docker tag  myalpine:v1.0 myalpine:v2.0
```
```
docker tag  myalpine:v2.0 myalpine:latest
```
```
docker images
```
```
docker login -u  <users> 
```
Donc  </br>
'<users> : sitrakaresearchandpoc'</br> </br>

créer une version : 
```
docker tag myalpine:v2.0 sitrakaresearchandpoc/myalpine:v2.0
```
```
docker push sitrakaresearchandpoc/myalpine:v2.0
```
 




