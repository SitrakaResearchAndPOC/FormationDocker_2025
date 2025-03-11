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

## Exercice 2 : NGINX
