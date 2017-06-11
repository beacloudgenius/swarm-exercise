### do this locally

```
docker build -t cg .
docker run -p 8080:80 cg
docker login
docker tag cg cloudgenius/friendlyname:tag
docker push cloudgenius/friendlyname:tag
docker run -p 8080:80 cloudgenius/friendlyname:tag
```
### Now take it to a cloud

```
docker-machine create --driver digitalocean \
    --digitalocean-access-token=75eef9cb757034e4e0114cc2fb677ae6a0e5dcc6230eb158d812b4c45754db96 \
    --digitalocean-size 2gb \
    myvm1

docker-machine ssh myvm1 "docker swarm init --advertise-addr  10.17.0.5"

docker stack deploy -c docker-compose.yml     app
docker stack ps app
```
Change replica count in docker-compose.yml and
```
docker stack deploy -c docker-compose.yml app
docker stack ps app
```

delete your app
```
docker stack rm app
```
