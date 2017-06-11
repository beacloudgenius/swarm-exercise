docker run -d -p 80:80 -p 443:443 \
        --name nginx-proxy \
        -v /root/certs:/etc/nginx/certs:ro \
        -v /etc/nginx/vhost.d \
        -v /usr/share/nginx/html \
        -v /var/run/docker.sock:/tmp/docker.sock:ro \
        jwilder/nginx-proxy



docker run -d \
    -v /root/certs:/etc/nginx/certs:rw \
    --volumes-from nginx-proxy \
    -v /var/run/docker.sock:/var/run/docker.sock:ro \
    jrcs/letsencrypt-nginx-proxy-companion



docker run -d \
       --name tutum-just-apache-app \
       -e "VIRTUAL_HOST=proper.cloudgeni.us" \
       -e "LETSENCRYPT_HOST=proper.cloudgeni.us" \
       -e "LETSENCRYPT_EMAIL=nilesh@cloudgeni.us" \
       tutum/apache-php
