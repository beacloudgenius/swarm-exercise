# Swarm exercise
```
docker build -t cg .
docker run -p 8080:80 cg
docker login
docker tag cg cloudgenius/friendlyname:tag
docker push cloudgenius/friendlyname:tag
docker run -p 8080:80 cloudgenius/friendlyname:tag

docker swarm init
docker stack deploy -c docker-compose.yml     appname
docker stack ps appname
```
Change replica count in docker-compose.yml and
```
docker stack deploy -c docker-compose.yml appname
docker stack rm appname
```


## Swarm exercise

#### In Windows 10 with docker installed
```
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm1
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm2
```
#### In Macintosh with docker-machine
```
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2
```
#### In Cloud Genius workstation
```
docker-machine create --driver digitalocean myvm1
docker-machine create --driver digitalocean myvm2

docker-machine create --driver digitalocean \
    --digitalocean-access-token=9c817458775c95c89f2937ca5ed7bf0d5e8f0edf9ddbbab3c5590accffc83bff \
    --digitalocean-size 2gb \
    myvm1
```

```
docker-machine ssh myvm1 "docker swarm init"
docker swarm init --advertise-addr 159.203.70.89
docker-machine ssh myvm2 "output from previous step"
docker swarm join-token manager
docker swarm join-token worker
```
