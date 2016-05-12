# What is Dory

Dory is a backup solution for docker. It is (for now) mainly oriented towards backuping to google cloud storage.

Aside from just backing up your data containers, dory can also schedule backuping and show you which containers were backed up and when.

# How to use it

## Setting up access to google cloud storage

The dory container uses three environment variables

GS_PROJECT_ID
GS_ACCESS_KEY
GS_SECRET_ACCESS_KEY

You have to get those values from the developers' console.

It is recommended to create your own container with those values already in the ENV

## Manually backup a container

```
docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock -e GS_PROJECT_ID=... ceymard/dory:latest backup <container_name>
```


## Automatize backups

Either use cron to manually perform backups, or run the dory server with google database.

# What it does

To backup a container, dory inspects its volumes, mounts them into a fake container in `/backup/...` and runs duplicity to your host of choice.

# The Web server

You can

* Browse all 

# Best practices

Have a data container (just like docker's doc say !) since the identifier of the backup takes the container hash in it -- it will change everytime you recreate it otherwise.

# I restored a backup, but I want to keep backuping in it, because I know it will be alone

Well, tough luck Johny, you'll have to back it up all over again.