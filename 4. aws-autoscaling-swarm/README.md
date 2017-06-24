## Caution:

Because of this issue https://github.com/docker/for-aws/issues/20
use edge https://editions-us-east-1.s3.amazonaws.com/aws/edge/Docker.tmpl
not stable https://editions-us-east-1.s3.amazonaws.com/aws/stable/Docker.tmpl

## Deploy auto-scaling swarm in aws

https://docs.docker.com/docker-for-aws/
```
docker node ls
```
https://docs.docker.com/docker-for-aws/deploy/

use this cg.tmpl in cloud formation

```
ssh -NL 127.0.0.1:2374:/var/run/docker.sock docker@leaderip   &
docker -H 127.0.0.1:2374 info     
export DOCKER_HOST=tcp://127.0.0.1:2374
docker info
```

## Test
```
openssl rand -base64 20 | docker secret create root_db_password -
openssl rand -base64 20 | docker secret create wp_db_password -

docker network create -d overlay wp

docker service create \
    --name mariadb \
    --replicas 1 \
    --constraint=node.role==manager \
    --network wp \
    --secret source=root_db_password,target=root_db_password \
    --secret source=wp_db_password,target=wp_db_password \
    -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/root_db_password \
    -e MYSQL_PASSWORD_FILE=/run/secrets/wp_db_password \
    -e MYSQL_USER=wp \
    -e MYSQL_DATABASE=wp \
    mariadb:10.3


docker service create \
   --name wp \
   --constraint=node.role==worker \
   --replicas 1 \
   --network wp \
   --publish 80:80 \
   --secret source=wp_db_password,target=wp_db_password,mode=0400 \
   --mount type=bind,source=/mnt/gluster/wp-content,destination=/var/www/html/wp-content \
   -e WORDPRESS_DB_USER=wp \
   -e WORDPRESS_DB_PASSWORD_FILE=/run/secrets/wp_db_password \
   -e WORDPRESS_DB_HOST=mariadb \
   -e WORDPRESS_DB_NAME=wp \
   wordpress:4.8



docker service create \
     --name=viz \
     --publish 8080:8080 \
     --constraint=node.role==manager \
     --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
     dockersamples/visualizer


docker service ps mariadb  

docker service ps wp

docker service ps viz

docker service update --replicas 2 wp


#directly connect to the leader to check logs

ssh root@

docker service logs wp
docker service logs mariadb

```

## Clean up Test
```
docker service remove wp mariadb viz
docker network rm wp
```

## Production scalable wordpress in docker swarm
```

openssl rand -base64 20 | tee root_db_password | docker secret create root_db_password -
openssl rand -base64 20 | tee wp_db_password   | docker secret create wp_db_password -
openssl rand -base64 20 | tee x                | docker secret create x -
docker network create -d overlay traefik
docker network create -d overlay mariadb
```
### for AWS EFS backed storage
```
docker stack deploy -c docker-compose.yml app
```
### for gluster backed storage
```
docker stack deploy -c docker-compose.local.yml app
```
### Manage the stack
```
docker stack ps app

docker service update --replicas 2 app_wp
docker service update --replicas 1 app_wp
docker service update --replicas 4 app_wp

docker stack rm app

```
## Debug
```
docker service ls

docker service ps <service-name>
docker service ps app_wp

docker inspect <task-id>
docker inspect k05kofo6i3v6

docker plugin install --alias cloudstor:aws --grant-all-permissions docker4x/cloudstor:aws-v17.03.1-ce-rc1-03_22_2017 CLOUD_PLATFORM=AWS EFS_ID_REGULAR=$EFS_ID_REGULAR

docker service inpect deh2gu2vuc7hw9o8lgg4ivfck

docker logs <container-id>
docker plugin ls
docker volume ls   

```
## Mount EFS locally

https://serverfault.com/questions/799016/elastic-file-system-efs-mount-outside-of-aws

https://serverfault.com/questions/800187/amazon-efs-mount-from-osx?noredirect=1&lq=1
