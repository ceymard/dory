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


## Backup all the containers marked for it

The web interface allows you to tell which containers should be backuped. (what about labels ?)

# What it does

To backup a container, dory inspects its volumes, stops all the containers that are linked to it, mounts them into a fake container in `/backup/...` and runs duplicity to your host of choice. Afterwards, it starts the containers it had stopped before.

(it should be possible to change this behaviour on a per-container basis)

# The Web server

You can

* Browse all the backups and see their status ;
    - Current backup size
    - Oldest backup entry date
    - Latest backup entry date
    - Backup start date (first time ever it was backuped)
    - Container name, container id
    - Server name (on which host the container was)
    - Whether the container is orphaned or not (as in ; the container that was being backuped disappeared from the machine)
    - Description of the container
    - List of all the possible restore points, along with descriptions for those that had one
* Restore a backup into another container (of a specific time) (maybe with a pub/sub thing? maybe we just want *one* web server and several slaves that just listen to google's pub/sub protocol)
* Delete a useless backup
* Rename a backup or change its server (useful if one knows what he is doing like migrating a container from a host to another)
* Name a backup point (for instance "backup before production reload 2016-02-10")
* Add some metadatas to a backup (description, ...)
* Browse the containers running on a machine to optionally mark one for backup
* Configure some custom duplicity options (like how much data to retain)
* See some data such as current total size (and maybe pricing ?)
* Launch an unscheduled backup
* Schedule backups
* See the pings of all the running dory servers (they coordinate through google ?)
* Configure emailing to send reports to (only one server should be in charge of that...), which tells you
    - Which containers were backuped an when exactly
    - Which containers are new
    - Which were removed
    - Which were orphaned

You can have as many servers running as you like ; you pay for google's database anyway.

# What does it not do ?

Dory does not backup a container running profile. You need to manage those yourself for instance with some `docker-compose.yml` files that you put on a git somewhere. It only cares about data containers, which generally don't change and just have 

# Best practices

Have a data container (just like docker's doc say !) since the identifier of the backup takes the container hash in it -- it will change everytime you recreate it otherwise.
