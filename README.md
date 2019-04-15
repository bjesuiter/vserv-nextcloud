# Vserv Nextcloud

Uses: [Nextcloud Docker](https://github.com/nextcloud/docker)

HTTPS done by: [vserv-reverse-proxy](https://bitbucket.org/bnware/vserv-reverse-proxy/src/master/)

Included by setting env vars in docker-compose:

      VIRTUAL_HOST: nextcloud.avil.io
      LETSENCRYPT_HOST: nextcloud.avil.io \
      LETSENCRYPT_EMAIL: bjesuiter@gmail.com \