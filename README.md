# Vserv Nextcloud

Uses: [Nextcloud Docker](https://github.com/nextcloud/docker)

HTTPS done by: [vserv-reverse-proxy](https://bitbucket.org/bnware/vserv-reverse-proxy/src/master/)

Included into Reverse Proxy by setting env var NEXTCLOUD_DOMAIN in .env file 
which gets used in docker-compose like this: 

      VIRTUAL_HOST: ${NEXTCLOUD_DOMAIN}
      LETSENCRYPT_HOST: ${NEXTCLOUD_DOMAIN}
      
# After install setup

- convert some table columns to big-int type: 
    - `npm run shell:nxt`
    - `./occ db:convert-filecache-bigint`
      
 ## Scripts 
 
 - start -> start this nextcloud system as detached containers with docker-compose 
 - stop -> stops all containers from this system 
 - stop:rm -> CAUTION: Stops all containers AND REMOVES ALL VOLUMES WITH ALL DATA (!!!)
 
 # Possible Cron Job improvement
 https://cronitor.io/?utm_source=crontabguru&utm_campaign=cron_failures
 
 ## Help
 
 - [Nextcloud on Docker Hub](https://hub.docker.com/_/nextcloud/)
 - [How to remove docker resources](https://linuxize.com/post/how-to-remove-docker-images-containers-volumes-and-networks/#remove-one-or-more-volumes)
 - [Nextcloud trusted domains option](https://github.com/nextcloud/docker/pull/500)
 