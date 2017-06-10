https://blog.maddevs.io/deploy-and-scale-wordpress-with-docker-cloud-swarm-mode-f780d4e735cb

use edge

https://editions-us-east-1.s3.amazonaws.com/aws/edge/Docker.tmpl

https://github.com/docker/for-aws/issues/20

not stable

https://editions-us-east-1.s3.amazonaws.com/aws/stable/Docker.tmpl


docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock -e DOCKER_HOST dockercloud/client cloudgenius/va

export DOCKER_HOST=tcp://127.0.0.1:32769

docker node ls

ID                           HOSTNAME                       STATUS  AVAILABILITY  MANAGER STATUS
0fti4troh7w1knny0dx2g88rr *  ip-172-31-44-80.ec2.internal   Ready   Active        Leader
gz7gyid1vq4mui3uldum7q2y1    ip-172-31-38-118.ec2.internal  Ready   Active
z3vp1y7qo7bjpkwm8976ft4nf    ip-172-31-17-200.ec2.internal  Ready   Active



ssh -NL localhost:2374:/var/run/docker.sock docker@34.207.189.201 &                                                                        

docker -H localhost:2374 info     

export DOCKER_HOST=tcp://127.0.0.1:2374

docker info

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

docker service ps mariadb  

docker service create \
   --name wp \
   --constraint=node.role==worker \
   --replicas 1 \
   --network wp \
   --publish 80:80 \
   --secret source=wp_db_password,target=wp_db_password,mode=0400 \
   -e WORDPRESS_DB_USER=wp \
   -e WORDPRESS_DB_PASSWORD_FILE=/run/secrets/wp_db_password \
   -e WORDPRESS_DB_HOST=mariadb \
   -e WORDPRESS_DB_NAME=wp \
   wordpress:4.8

docker service ps wp
docker service remove wp mariadb


docker network create -d overlay traefik
docker network create -d overlay mariadb


docker stack deploy -c docker-compose.yml wp




debug

docker service ls

docker service ps <service-name>
docker service ps wp_wp

docker inspect <task-id>
docker inspect k05kofo6i3v6

docker plugin install --alias cloudstor:aws --grant-all-permissions docker4x/cloudstor:aws-v17.03.1-ce-rc1-03_22_2017 CLOUD_PLATFORM=AWS EFS_ID_REGULAR=$EFS_ID_REGULAR

docker service inpect deh2gu2vuc7hw9o8lgg4ivfck

docker logs <container-id>
docker plugin ls
docker volume ls   
