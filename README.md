## Install
```
git clone repo
sudo su -
crontab -e
add:
0 2 * * * docker exec gitlab_gitlab_1 sh -c 'gitlab-rake gitlab:backup:restore' CRON=1
docker-compose build
docker-compose up gitlab
sleep 60 seconds to allow bootstrapping
docker-compose up gitlab-runner1
log into gitlab and setup root account, get runner config key and external url.
docker exec -it gitlab-runner1 gitlab-runner register
```

## Backups
Backups are stores in ``/var/opt/gitlab/backups``. They contain all repositories and data around gitlab. We should
also backup the /etc/gitlab directory. The directory contains the database encryption secrets and 
the general configuration of gitlab. 
```
Use skip to ignore certain pieces
docker exec -it gitlab gitlab-rake gitlab:backup:create SKIP=db,uploads

Additonal flags
SKIP=db, uploads,repositories,builds,artifacts,lfs, registry, pages
BACKUP=dump [Changes the back up filename]
GZIP_RSYNCABLE=yes [Allows Rsyncing of backup]
STRATEGY=copy [Copies full data dump prior to backing it up. for actice systems]
CRON=1 [Suppress output if no erros to reduce log spam]
```

## Restore
```
docker exec -it gitlab gitlab-rake gitlab:backup:restore
```