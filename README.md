# Vserv Nextcloud

Uses: [Nextcloud Docker](https://github.com/nextcloud/docker)

- [Guide: Using Object Storage as Primary Storage](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/primary_storage.html)
- [ENV Vars available in nextcloud docker image](https://hub.docker.com/_/nextcloud?tab=description)

## Workflows

### Running Postgres Commands inside db container

1. Open a shell to db container with `bonnie db-shell`
2. See all locked files via: `psql -U $POSTGRES_USER -d $POSTGRES_DB -c "SELECT * FROM oc_file_locks WHERE lock=1"`
3. Delete all file locks: `psql -U $POSTGRES_USER -d $POSTGRES_DB -c "DELETE FROM oc_file_locks WHERE lock=1"`

---

# CLEANUP README!!!

## TODO

- make dev doppler config working locally her, with ./playground as data folder

## Advanced Config Guides

- https://wiki.archlinux.org/title/Nextcloud#Writable_apps_directory

## Backup & Restore

### Official Nextcloud

- [Backup Guide](https://docs.nextcloud.com/server/latest/admin_manual/maintenance/backup.html)
- [Restore Guide](https://docs.nextcloud.com/server/latest/admin_manual/maintenance/restore.html)

Note: run `occ maintenance:data-fingerprint` after restauration!

### Benjamin Jesuiter Backups

- Files: via minio mc mirror from Netcup storage1500 to aws s3
- Folders (Config, Themes, etc.): backup to jb-web-services/backups/nextcloud
- Database: backup via pg_dump to jb-web-services/backups/nextcloud

### New: Database and Service Folder Backups using restic to AWS S3!

1.[One-Time] Create new Bucket for storing these restic backups

- Account: bjesuiter@gmail.com, with IAM: admin@bjesuiter
- Bucket-Name: `nextcloud.avil.io-restic`
- ACLs deaktiviert
- Gesamten öffentlichen Zugriff deaktivieren
- Bucket-Versioning dekativiert
- Tags:
  - server-alias=epyc
  - server-provider=netcup
  - vserv-app=nextcloud
- Keine Verschlüsselung (Restic verschlüsselt eh schon)
- Keine Objektsperre

2. [One-Time] Create an IAM User for accessing this bucket

   - Name: restic-nextcloud-epyc
   - With Policy:

   ```
   {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::nextcloud.avil.io-restic"
        }
    ]
   }
   ```

3. [One-Time] Setup Env Vars in doppler.com project
   (All secret info can be found in Bitwarden in note `[BACKUP BUCKET] nextcloud.avil.io-restic`)

   - Open doppler.com with `bonnie open-doppler`
   - Setup `RESTIC_REPOSITORY` as `s3:s3.amazonaws.com/nextcloud.avil.io-restic`
   - Setup `AWS_ACCESS_KEY_ID` and `AWS_ACCESS_KEY_SECRET` from Bitwarden
   - Setup `RESTIC_PASSWORD` from Bitwarden

4. [One-Time] Init Restic Repository manually from mac
   _(if not available: Install restic via: `brew install restic`)_

   ```
   # Note: Restic repository and S3 Access Keys are provided by doppler
   doppler run -- restic init
   ```

## Interacting with Restic Backups

_All connection data will be auto-filled by doppler_  
_If the bonnie executable is not available, run doppler directly like `doppler run -- restic`_

- `bonnie restic snapshot` => shows all available snapshots

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
