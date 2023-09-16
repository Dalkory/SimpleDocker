FROM nginx:latest
USER root
WORKDIR /home/

COPY server/nginx/nginx.conf /etc/nginx/
COPY server/server.c /home/server.c
COPY start.sh /home/start.sh

RUN apt-get update; \
    apt-get install -y gcc spawn-fcgi libfcgi-dev; \
    chmod +x start.sh; \
    chmod 1755 /usr/bin/wall; \
    chmod 1755 /usr/sbin/unix_chkpwd; \
    chmod 1755 /usr/bin/expiry; \
    chmod 1755 /usr/bin/chage; \
    chmod 0755 /usr/bin/chfn; \
    chmod 0755 /usr/bin/su; \
    chmod 0755 /usr/bin/chsh; \
    chmod 0755 /usr/bin/umount; \
    chmod 0755 /usr/bin/mount; \
    chmod 0755 /usr/bin/passwd; \
    chmod 0755 /usr/bin/newgrp; \
    chmod 0755 /usr/bin/gpasswd; \
    rm -rf /var/lib/apt/lists
    
HEALTHCHECK NONE

ENTRYPOINT bash start.sh