on manager

sudo apt update
sudo apt install -y nfs-kernel-server

sudo mkdir -p /exports/www
sudo chown www-data:www-data /exports/www

Edit /etc/exports and add:

/exports/www  10.99.0.0/24(rw,sync,no_subtree_check)


sudo service nfs-kernel-server restart

allowing TCP connections on ports 111 and 2049 from any IP address inside your private network.



on workers


sudo apt install nfs-common
sudo mkdir -p /var/www
sudo mount manager:/exports/www /var/www

edit the /etc/fstab file on all workers to automatically mount /exports/www from the NFS server at boot, and add the following:

manager:/exports/www /var/www nfs defaults 0 0

Substitute manager with the actual IP address of your NFS server.



try this



docker volume create --driver local \
    --opt type=nfs \
    --opt o=addr=192.168.1.1,rw \
    --opt device=:/path/to/dir \
    foo

version: '2'
volumes:
  nfs:
    driver: local
    driver_opts:
      type: nfs
      o: addr=192.168.1.1,rw
      device: ":/path/to/dir"
services:
  php-fpm:
    volumes:
      - nfs:/path/in/container
    ...    


and this https://www.vip-consult.solutions/post/persistent-storage-docker-swarm-nfs#content


swarm-exec \
        docker run -d \
        --privileged --pid=host \
        --restart=unless-stopped \
     -e SERVER=?????:/  -e MOUNT=/host/mount/folder vipconsult/moby-nfs-mount 
