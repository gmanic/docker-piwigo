# This project is partially maintained.

---
History:
Mathieu stopped mainting due to:
I've stopped using piwigo due to bugs never fixed. 
You can export and rebuild your virtual albums structure into file directory with this tools: https://github.com/mathieuruellan/piwigo-exporter
---

I have forked his repo and update this one to current piwigo version, as far as possible.
Latest Changes (latest first):

2026-11-20
- upgrade version to 16.5.0.2 to include php85-fpm-alpine3.24 - (php: 8.5.13)

2026-11-17
- upgrade piwigo-version to 16.5.0, incl php85-fpm-alpine3.24 (php:fpm-alpine)

earlier
- upgrade piwigo-Version to 16.4.0
- upgrade to php84-fpm-alpine (which is 3.23)
(currently trying to implement fully automated docker-builds including tagging based on versions)
(not successful, so far...)

# Piwigo-Docker

This is an image for piwigo, linked with a mysql database, and a special volumes setup.
Data must be stored on a volume (see Dockerfile for details, inherited from Mathieu's original def).

The follwing mounts are supported:
/var/www/galleries
/var/www/themes
/var/www/plugins
/var/www/local
/var/www/_data/i
/config

Technically, if those directories are available (as in: being mounted inside the convtainer),
the content is used. If a directory is missing, the piwigo release-zip-file is used.

By this, you can split and include other themes, plugins, and cache-dir (_data/i), if needed.


## Features
- Easy deployment of Piwigo with a docker-compose.

## Deployment

Word of warning. I do not test a completely fresh install of the image. It used by my personal
gallery. Use at your own risk, do your own research, ensure proper security yourself. Author
takes no responsibility.

Maybe this still works:

Edit this `docker-compose.yml` and launch with the command 

```
mkdir -p ./piwigo/data/local/config
chmod -R 0777 ./piwigo
docker-compose up -d 
```

```
services:
  mariadb:
    image: mariadb:latest
    volumes:
        - ./piwigo/mysql/:/var/lib/mysql
    environment:
        - MARIADB_ROOT_PASSWORD=piwigo
        - MARIADB_DATABASE=piwigo
        - MARIADB_USER=piwigo
        - MARIADB_PASSWORD=piwigo
  piwigo:
    image: gmaniac/piwigo
    environment:
        - BASH_MODE=set -e
    depends_on:
        - mariadb
    volumes:
        - ./piwigo/data/galleries:/var/www/galleries
        - ./piwigo/data/local:/var/www/local
        - ./piwigo/data/plugins:/var/www/plugins
        - ./piwigo/data/themes:/var/www/themes
        - ./piwigo/cache:/var/www/_data/i
        - ./piwigo/upload:/var/www/upload
        - ./var/log:/var/log
        - ./var/log/piwigo:/var/log/apache2
    ports:
        - "9999:80"
    hostname: localhost
    domainname: mydomain

```

After db initialization (first launch), environment variables can me removed.


EDIT: installation with version 13.x from scratch  seems to be broken.
Install with mathieuruellan/piwigo:12.3.0 first and upgrade
