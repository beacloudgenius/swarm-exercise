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
    --digitalocean-access-token=75eef9cb757034e4e0114cc2fb677ae6a0e5dcc6230eb158d812b4c45754db96 \
    --digitalocean-size 1gb \
    myvm1
```

```
docker-machine ssh myvm1 "docker swarm init"
docker swarm init --advertise-addr 159.203.70.89
docker-machine ssh myvm2 "output from previous step"
docker swarm join-token manager
docker swarm join-token worker
```
