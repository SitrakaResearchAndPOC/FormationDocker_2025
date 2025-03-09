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
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
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
docker ps -a
```
```  
docker run -d nginx:latest
```  


