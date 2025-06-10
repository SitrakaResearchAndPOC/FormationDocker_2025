# INSTALLATION DES 3 MACHINES CLUSTER & HOSTNAME & IN SAME NETWORK 
* If you use all in one VM, use VM NAT or Network NAT (Network + NAT)
* If you use external configuration, use bridge
* For this tutorial (ubuntu 22.04)
## For all cluster
Terminal clusterswarm1 & clusterswarm2 & clusterswarm3
```
apt update 
```
```
apt install docker.io openssh-server
```
```
systemctl start ssh
```
```
systemctl enable ssh
```
## Configure hostname for clusterswarm1
Terminal clusterswarm1
```
nano /etc/hosts
```
change 
```
127.0.0.1 ubuntu
```
to clusterswarm1
```
127.0.0.1 clusterswarm1
```
and Tape ctrl+x and Yes for saving
```
sudo hostnamectl set-hostname clusterswarm1
```
Verify hostname by 
```
hostname
```

## Configure hostname for clusterswarm2
Terminal clusterswarm2
```
nano /etc/hosts
```
change 
```
127.0.0.1 ubuntu
```
to clusterswarm2
```
127.0.0.1 clusterswarm2
```
And Tape ctrl+x and Yes for saving
```
sudo hostnamectl set-hostname clusterswarm2
```
```
hostname
```

## Configure hostname for clusterswarm3
Terminal clusterswarm3
```
nano /etc/hosts
```
change 
```
127.0.0.1 ubuntu
```
to clusterswarm3
```
127.0.0.1 clusterswarm3
```
and tape Tape ctrl+x and Yes for saving
```
sudo hostnamectl set-hostname clusterswarm3
```
```
hostname
```

## Launching manager
Terminal clusterswarm1
```
docker info
```
```
docker swarm leave --force
```
```
docker swarm init --advertise-addr <ip_clusterswarm1>
```
option : --listen-addr exist also </br>
copy the output as : docker join ... </br>
and add it on nano dockerjoin.txt
```
nano dockerjoin.txt
```
Past the command and Tape ctrl+x and Yes for saving
```
scp dockerjoin.txt clusterswarm2@<ip_clusterswarm2>:/home/clusterswarm2
```
Tape password
```
scp dockerjoin.txt clusterswarm2@<ip_clusterswarm3>:/home/clusterswarm3
```
Tape password
## Join the worker in clusterswarm2
Terminal clusterswarm2
```
cat dockerjoin.txt
```
Copy and launch docker join ...
## Join the worker in clusterswarm3
Terminal clusterswarm3
```
cat dockerjoin.txt
```
Copy and launch docker join ...
## Testing service foo for ping
```
nano print_services.sh
```
Paste the script bash for finding the node for each service 
```
#!/bin/bash
# Lister tous les services avec leur ID et leur nom
docker service ls --format "{{.ID}}\t{{.Name}}" | while read service_id service_name; do
    echo -e "\nService: $service_name (ID: $service_id)"
    
    # Lister les tâches en état 'running' pour chaque service
    docker service ps $service_id --no-trunc --filter "desired-state=running" --format "{{.ID}}\t{{.Node}}\t{{.CurrentState}}" | while read task_id node_id current_state; do
        # Récupérer le nom du nœud à partir de son ID
        node_name=$(docker node inspect --format '{{.Description.Hostname}}' $node_id)
        
        # Afficher les informations sur la tâche active
        echo -e "\tTask: $task_id | Node: $node_name (ID: $node_id) | CurrentState: $current_state"
    done
done
```
Tape ctrl+x and Yes for saving
```
docker node ls
```
```
docker service create --name foo alpine ping 8.8.8.8
```
```
docker service ls 
```
```
docker service ps foo
```
```
docker service logs foo
```
```
bash print_services.sh
```
```
docker service rm foo
```

## Testing service whoami
```
docker service create --name web -p 80:80 emilevauge/whoami
```
```
docker service ps web
```
```
bash print_services.sh
```
```
docker service rm web
```

## Testing service rolling update & rolling back & draining exquisite web

```
docker network create --driver overlay --opt encrypted exquisite
```
```
docker network ls
```
```
docker service create --name back --network exquisite --limit-memory 64M --reserve-memory 64M vdemeester/exquisite-words-java:v1
```
```
docker service ls
```
```
docker service create --name mongo --network exquisite mongo:3.3.8
```
```
docker service create --name front --network exquisite -p 800:80 vdemeester/exquisite-web:v1
```
```
docker service scale back=15
```
```
bash print_services.sh
```
```
docker service ls
```
```
docker service ps back
```
```
docker service scale front=2
```
```
bash print_services.sh
```
```
docker service ls
```
```
docker service ps front
```
```
docker service update --update-parallelism 2 --update-delay 10s front
```
```
docker service update --image vdemeester/exquisite-web:v2 front
```
```
bash print_services.sh
```
```
docker service update --update-parallelism 2 --update-delay 10s back
```
```
docker service update --image vdemeester/exquisite-words-java:v2  back
```
```
bash print_services.sh
```
```
docker node update --availability drain <idhost_clusterswarm3>
```
Other options ---> drain, active, pause
```
bash print_services.sh
```

```
docker service create svc1 --contrainst engine.label==ssd nginx
```
## Testing service web
```
docker service create --replicas 10 --name web  -p 80:80 nginx
```
```
docker node ps 
```



