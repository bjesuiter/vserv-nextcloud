# Vserv Nextcloud

Uses: [Nextcloud Docker](https://github.com/nextcloud/docker)

HTTPS done by: [vserv-reverse-proxy](https://bitbucket.org/bnware/vserv-reverse-proxy/src/master/)

Included into Reverse Proxy by setting env var NEXTCLOUD_DOMAIN in .env file 
which gets used in docker-compose like this: 

      VIRTUAL_HOST: ${NEXTCLOUD_DOMAIN}
      LETSENCRYPT_HOST: ${NEXTCLOUD_DOMAIN}
      
 ## Scripts 
 
 - start -> start this nextcloud system as detached containers with docker-compose 
 - stop -> stops all containers from this system 
 - stop:rm -> CAUTION: Stops all containers AND REMOVES ALL VOLUMES WITH ALL DATA (!!!)