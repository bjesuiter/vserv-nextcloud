# Changelog

## v3.0.0 - 2021-11-01

- switch from mariadb to postgres: 13.4
- switch from local storage to minio object storage on minio.storage1500.vserv.fun
  - this storage is mirrored to aws s3
- fresh setup of nextcloud 22 after update deadlock disaster between 20 and 22

## v2.0.0 - 2021-10-26

- first tagged release
- compatible to vserv-traefik@v2.1.0 (with new ssl handling)
- add HOST_DOMAIN variable (which is like epyc.vserv.fun = infrastrucure address)
