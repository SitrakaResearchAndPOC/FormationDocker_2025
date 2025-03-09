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
exit
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
