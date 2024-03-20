---
title: Media Management
description: 
published: 1
date: 2024-03-20T06:47:16.131Z
tags: 
editor: markdown
dateCreated: 2024-03-20T06:40:18.922Z
---

# Media Management

> - [Immich](https://github.com/immich-app/immich?tab=readme-ov-file): High-performance self-hosted photo and video backup solution. 
> - [SABnzbd](https://github.com/sabnzbd/sabnzbd): SABnzbd is an open-source Usenet client facilitating file downloads from Usenet newsgroups, streamlining access to and retrieval of content from the Usenet network.  
> 
> Each component includes `compose.yaml` and `.env` files configuration.
{.is-info}


### Immich

<details id="bkmrk-compose.yaml-version"><summary>compose.yaml</summary>

```yaml
version: "3.8"

#
# WARNING: Make sure to use the docker-compose.yml of the current release:
#
# https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
#
# The compose file on main may not be compatible with the latest release.
#

name: immich

services:
  immich-server:
    container_name: immich_server
    image: ghcr.io/immich-app/immich-server:${IMMICH_VERSION:-release}
    command: [ "start.sh", "immich" ]
    volumes:
      - ${UPLOAD_LOCATION}:/usr/src/app/upload
      - /etc/localtime:/etc/localtime:ro
    env_file:
      - .env
    ports:
      - 2283:3001
    depends_on:
      - redis
      - database
    restart: always

  immich-microservices:
    container_name: immich_microservices
    image: ghcr.io/immich-app/immich-server:${IMMICH_VERSION:-release}
    # extends:
    #   file: hwaccel.yml
    #   service: hwaccel
    command: [ "start.sh", "microservices" ]
    volumes:
      - ${UPLOAD_LOCATION}:/usr/src/app/upload
      - /etc/localtime:/etc/localtime:ro
    env_file:
      - .env
    depends_on:
      - redis
      - database
    restart: always

  immich-machine-learning:
    container_name: immich_machine_learning
    image: ghcr.io/immich-app/immich-machine-learning:${IMMICH_VERSION:-release}
    volumes:
      - model-cache:/cache
    env_file:
      - .env
    restart: always

  redis:
    container_name: immich_redis
    image: redis:6.2-alpine@sha256:80cc8518800438c684a53ed829c621c94afd1087aaeb59b0d4343ed3e>
    restart: always

  database:
    container_name: immich_postgres
    image: tensorchord/pgvecto-rs:pg14-v0.1.11
    env_file:
      - .env
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_USER: ${DB_USERNAME}
      POSTGRES_DB: ${DB_DATABASE_NAME}
    volumes:
      - pgdata:/var/lib/postgresql/data
    restart: always

volumes:
  pgdata:
  model-cache:
```

</details><details id="bkmrk-.env-%23-the-location-"><summary>.env</summary>

```
# The location where your uploaded files are stored
UPLOAD_LOCATION=op://Secrets-Management/Immich/IMMICH_UPLOAD_LOCATION

# Connection secrets for postgres and typesense. You should change these to random passwords
TYPESENSE_API_KEY=op://Secrets-Management/Immich/IMMICH_TYPESENSE_API
DB_PASSWORD=op://Secrets-Management/Immich/IMMICH_TYPESENSE_DB_PASS

# The values below this line do not need to be changed
###################################################################################
DB_HOSTNAME=op://Secrets-Management/Immich/IMMICH_DB_HOSTNAME
DB_USERNAME=op://Secrets-Management/Immich/IMMICH_DB_USERNAME
DB_DATABASE_NAME=op://Secrets-Management/Immich/IMMICH_DB_NAME

REDIS_HOSTNAME=op://Secrets-Management/Immich/IMMICH_REDIS_HOSTNAME
```

</details>

### SABnzbd

<details id="bkmrk-compose.yaml-----ver"><summary>compose.yaml</summary>

```yaml
---
version: "2.1"
services:
  sabnzbd:
    image: ${SAB_IMAGE}
    logging:
      driver: ${SAB_DRIVER}
      options:
        gelf-address: ${SAB_GELF_ADDRESS}
    container_name: sabnzbd
    environment:
      - PUID=${SAB_PUID}
      - PGID=${SAB_PGID}
      - TZ=${SAB_TZ}
    volumes:
      - ${SAB_VOL_CONFIG}:/config
      - ${SAB_VOL_INCOMPLETE}:/incomplete-downloads
      - ${SAB_SHARED}
    ports:
      - 9090:8080
    restart: unless-stopped
```

</details><details id="bkmrk-.env-sab_image%3Dop%3A%2F%2F"><summary>.env</summary>

```
SAB_IMAGE=op://Secrets-Management/sabnzbd/SAB_IMAGE
SAB_SHARED=op://Secrets-Management/NAS/Volume_Mappings/SAB_SHARED

SAB_DRIVER=op://Secrets-Management/sabnzbd/SAB_DRIVER
SAB_GELF_ADDRESS=op://Secrets-Management/sabnzbd/SAB_GELF_ADDRESS

SAB_PUID=op://Secrets-Management/sabnzbd/SAB_PUID
SAB_PGID=op://Secrets-Management/sabnzbd/SAB_PGID
SAB_TZ=op://Secrets-Management/sabnzbd/SAB_TZ

SAB_VOL_CONFIG=op://Secrets-Management/sabnzbd/SAB_VOL_CONFIG
SAB_VOL_INCOMPLETE=op://Secrets-Management/sabnzbd/SAB_VOL_INCOMPLETE
```

</details>