# Vserv Nextcloud

Uses: [Nextcloud Docker](https://github.com/nextcloud/docker)

- [Guide: Using Object Storage as Primary Storage](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/primary_storage.html)
- [ENV Vars available in nextcloud docker image](https://hub.docker.com/_/nextcloud?tab=description)

## Advanced Config Guides

- https://wiki.archlinux.org/title/Nextcloud#Writable_apps_directory

## Backup & Restore

- [Backup Guide](https://docs.nextcloud.com/server/latest/admin_manual/maintenance/backup.html)
- [Restore Guide](https://docs.nextcloud.com/server/latest/admin_manual/maintenance/restore.html)

Note: run `occ maintenance:data-fingerprint` after restauration!

- Files: via minio mc mirror from Netcup storage1500 to aws s3
- Folders (Config, Themes, etc.): backup to jb-web-services/backups/nextcloud
- Database: backup via pg_dump to jb-web-services/backups/nextcloud

---

# Old

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

## Possible Cron Job improvement

- Monitor Cron Jobs: https://cronitor.io/?utm_source=crontabguru&utm_campaign=cron_failures

- Handling Cron Container: https://github.com/nextcloud/docker/pull/220

## Nextcloud Updates

1. Use newer nextcloud version docker image in docker-compose.yaml (z.B. 23-apache)

- CAUTION: Use only the next higher version, multi-version upgrade is not supported by nextcloud!
- NOTE: This sets nextcloud automatically into maintenance mode

2. Run upgrades via command line `npm run occ:upgrade`

## FAQ

### Nextcloud in Maintenance Mode?

Fix with:

- https://www.hostflash.de/blog/webhosting/nextcloud-wartungsmodus-aktivieren-und-deaktivieren-anleitung.html#
- https://help.nextcloud.com/t/gelost-nextcloud-im-wartungsmodus-seit-update-noob/74945

Deactivate with `npm run occ:maintenance-off`

### Docker Nextcloud Update deadlock

https://help.nextcloud.com/t/deadlock-while-upgrading-nextcloud-on-docker/125301

=> Only solution:

- Restore old DB
- Update to correct version first

### Download

## Help

- [Nextcloud on Docker Hub](https://hub.docker.com/_/nextcloud/)
- [How to remove docker resources](https://linuxize.com/post/how-to-remove-docker-images-containers-volumes-and-networks/#remove-one-or-more-volumes)
- [Nextcloud trusted domains option](https://github.com/nextcloud/docker/pull/500)
- [Build Cronjob Definitions](https://crontab.guru/#*/15_*_*_*_*)

## Useful Commands

- set nextcloud trusted domains while running:

       ./occ config:system:set trusted_domains 2 --value=nextcloud.avil.io nextcloud
