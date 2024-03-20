---
title: arr suite, misc.
description: 
published: 1
date: 2024-03-20T06:54:09.146Z
tags: 
editor: markdown
dateCreated: 2024-03-20T06:52:03.205Z
---

# *arr Suite, misc.

> [Radarr](https://radarr.video): Movie management software for organizing and managing movie libraries.  
> [Bazarr](https://www.bazarr.media): Subtitle management tool for handling subtitles associated with media files.  
> [Sonarr](https://sonarr.tv): TV show management application for organizing and tracking television show collections.  
> [Wizarr](https://github.com/wizarrrr/wizarr): Automatically invites users to Plex and Jellyfin Media Servers with unique links. Guides users through signup, offers client downloads, and instructions for media software  
>   
> Each component includes `compose.yaml` and `.env` files for configuration.
{.is-info}


## Radarr

<details id="bkmrk-compose.yaml-----ver"><summary>compose.yaml</summary>

```yaml
---
version: "2.1"
services:
  radarr:     # Normal Resolution Instance (720p/1080p)
    image: ${RADARR_IMAGE}
    logging:
      driver: ${RADARR_DRIVER}
      options:
        gelf-address: ${RADARR_GELF_ADDRESS}
    labels:
      - ${RADARR_LABEL1}
      - ${RADARR_LABEL2}
    container_name: radarr
    environment:
      - PUID=${RADARR_PUID}
      - PGID=${RADARR_PGID}
      - TZ=${RADARR_TZ}
      - TDARR_URL=${TDARR_URL}
      - TDARR_DB_ID=${RADARR_TDARR_DB_ID}
      - TDARR_PATH_TRANSLATE=${RADARR_TDARR_TRANSLATE}
    volumes:
      - ${RADARR_VOL_CONFIG}:/config
      - ${RADARR_SHARED}:/data
    ports:
      - 7878:7878				   # differing port mappings
    restart: unless-stopped

  radarr2:    # 4K Resolution Instance (4K/2160p)
    image: ${RADARR_IMAGE}
    logging:
      driver: ${RADARR_DRIVER}
      options:
        gelf-address: ${RADARR_GELF_ADDRESS}
    labels:
      - ${RADARR4K_LABEL1}
      - ${RADARR4K_LABEL2}
    container_name: radarr4k
    environment:
      - PUID=${RADARR4K_PUID}
      - PGID=${RADARR4K_PGID}
      - TZ=${RADARR_TZ}
      - DOCKER_MODS=${RADARR4K_DOCKER_MOD}
      - TP_ADDON=${RADARR4K_TP_ADDON}
      - TDARR_URL=${TDARR_URL}
      - TDARR_DB_ID=${RADARR4K_TDARR_DB_ID}
      - TDARR_PATH_TRANSLATE=${RADARR4K_TDARR_TRANSLATE}   
    volumes:
      - ${RADARR4K_VOL_CONFIG}:/config
      - ${RADARR_SHARED}:/data
    ports:
      - 7879:7878				# differing port mappings
    restart: unless-stopped
```

</details><details id="bkmrk-.env-%23-shared-radarr"><summary>.env</summary>

```
# Shared
RADARR_DRIVER=op://Secrets-Management/Radarr/Shared/RADARR_DRIVER
RADARR_GELF_ADDRESS=op://Secrets-Management/Radarr/Shared/RADARR_GELF_ADDRESS
RADARR_IMAGE=op://Secrets-Management/Radarr/Shared/RADARR_IMAGE
RADARR_TZ=op://Secrets-Management/Radarr/Shared/RADARR_TZ
RADARR_SHARED=op://Secrets-Management/NAS/Volume_Mappings/RADARR_SHARED
TDARR_URL=op://Secrets-Management/Tdarr/TDARR_URL

# Radarr
RADARR_LABEL1=op://Secrets-Management/Radarr/Radarr/RADARR_LABEL1
RADARR_LABEL2=op://Secrets-Management/Radarr/Radarr/RADARR_LABEL2
RADARR_PUID=op://Secrets-Management/Radarr/Radarr/RADARR_PUID
RADARR_PGID=op://Secrets-Management/Radarr/Radarr/RADARR_PGID
RADARR_TDARR_DB_ID=op://Secrets-Management/Radarr/Radarr/RADARR_TDARR_DB_ID
RADARR_TDARR_TRANSLATE=op://Secrets-Management/Radarr/Radarr/RADARR_TDARR_TRANSLATE
RADARR_VOL_CONFIG=op://Secrets-Management/Radarr/Radarr/RADARR_VOL_CONFIG

# Radarr4k
RADARR4K_LABEL1=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_LABEL1
RADARR4K_LABEL2=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_LABEL2
RADARR4K_PUID=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_PUID
RADARR4K_PGID=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_PGID
RADARR4K_TDARR_DB_ID=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_TDARR_DB_ID
RADARR4K_TDARR_TRANSLATE=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_TDARR_TRANSLATE
RADARR4K_VOL_CONFIG=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_VOL_CONFIG
RADARR4K_DOCKER_MOD=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_DOCKER_MOD
RADARR4K_TP_ADDON=op://Secrets-Management/Radarr/Radarr4K/RADARR4K_TP_ADDON
```

</details>

## Bazarr

<details id="bkmrk-compose.yaml-----ver-1"><summary>compose.yaml</summary>

```yaml
---
version: "2.1"
services:
  bazarr:     # Normal Resolution Instance (720p/1080p)
    image: ${BAZARR_IMAGE}
    container_name: bazarr
    environment:
      - PUID=${BAZARR_PUID}
      - PGID=${BAZARR_PGID}
      - TZ=${BAZARR_TZ}
    volumes:
      - ${BAZARR_VOL_CONFIG}:/config          # volume mounted configuration folder
      - ${BAZARR_SHARED}
    logging:
      driver: ${BAZARR_DRIVER}
      options:
        gelf-address: ${BAZARR_GELF_ADDRESS}
    ports:
      - 6767:6767    # port mappings have to be different between installations
    restart: unless-stopped
  
  bazarr2:    # 4K Resolution (4K/2160p)
    image: ${BAZARR_IMAGE}
    container_name: bazarr4k
    environment:
      - PUID=${BAZARR4K_PUID}
      - PGID=${BAZARR4K_PGID}
      - TZ=${BAZARR_TZ}
      - DOCKER_MODS=${BAZARR4K_DOCKER_MOD}
      - TP_ADDON=${BAZARR4K_TP_ADDON}
    volumes:
      - ${BAZARR4K_VOL_CONFIG}:/config       # volume mounted configuration folder
      - ${BAZARR_SHARED} 
    logging:
      driver: ${BAZARR_DRIVER}
      options:
        gelf-address: ${BAZARR_GELF_ADDRESS}
    ports:
      - 6769:6767     # port mappings have to be different between installations
    restart: unless-stopped
```

</details><details id="bkmrk-.env-%23-shared-bazarr"><summary>.env</summary>

```
# Shared
BAZARR_DRIVER=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Shared/BAZARR_DRIVER
BAZARR_GELF_ADDRESS=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Shared/BAZARR_GELF_ADDRESS
BAZARR_IMAGE=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Shared/BAZARR_IMAGE
BAZARR_TZ=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Shared/BAZARR_TZ
BAZARR_SHARED=op://Secrets-Management/NAS/Volume_Mappings/BAZARR_SHARED
# ----------------------------------------------
# Bazarr
BAZARR_PUID=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr/BAZARR_PUID
BAZARR_PGID=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr/BAZARR_PGID

BAZARR_VOL_CONFIG=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr/BAZARR_VOL_CONFIG
# ----------------------------------------------
# Bazarr4K
BAZARR4K_PUID=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr4K/BAZARR4K_PUID
BAZARR4K_PGID=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr4K/BAZARR4K_PGID

BAZARR4K_VOL_CONFIG=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr4K/BAZARR4K_VOL_CONFIG

BAZARR4K_DOCKER_MOD=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr4K/BAZARR4K_DOCKER_MOD
BAZARR4K_TP_ADDON=op://Secrets-Management/srdonfpn6izjbbfwpkc7y4o3uy/Radarr4K/BAZARR4K_TP_ADDON
```

</details>

## Sonarr

<details id="bkmrk-compose.yaml-service"><summary>compose.yaml</summary>

```yaml
services:
  sonarr:     # Normal Resolution Instance (720p/1080p)
    image: ${SONARR_IMAGE}
    logging:
      driver: ${SONARR_DRIVER}
      options:
        gelf-address: ${SONARR_GELF_ADDRESS}
    container_name: sonarr
    environment:
      - PUID=${SONARR_PUID}
      - PGID=${SONARR_PGID}
      - TZ=${SONARR_TZ}
      - TDARR_URL=${TDARR_URL}
      - TDARR_DB_ID=${SONARR_TDARR_DB_ID}
      - TDARR_PATH_TRANSLATE=${SONARR_TDARR_TRANSLATE}
    volumes:
      - ${SONARR_VOL_CONFIG}:/config
      - ${SONARR_SHARED}:/data
    ports:
      - 8989:8989                               # port mappings have to be different between installations
    restart: unless-stopped



  sonarr2:    # 4K Resolution (4K/2160p)
    image: ${SONARR_IMAGE}
    logging:
      driver: ${SONARR_DRIVER}
      options:
        gelf-address: ${SONARR_GELF_ADDRESS}
    container_name: sonarr4k
    environment:
      - PUID=${SONARR4K_PUID}
      - PGID=${SONARR4K_PGID}
      - TZ=${SONARR_TZ}
      - DOCKER_MODS=${SONARR4K_DOCKER_MOD}
      - TP_ADDON=${SONARR4K_TP_ADDON}
      - TDARR_URL=${TDARR_URL}
      - TDARR_DB_ID=${SONARR4K_TDARR_DB_ID}
      - TDARR_PATH_TRANSLATE=${SONARR4K_TDARR_TRANSLATE}
    volumes:
      - ${SONARR4K_VOL_CONFIG}:/config
      - ${SONARR_SHARED}:/data
    ports:
      - 8889:8989                               # port mappings have to be different between installations
    restart: unless-stopped
```

</details><details id="bkmrk-.env-%23-shared-sonarr"><summary>.env</summary>

```
# Shared
SONARR_DRIVER=op://Secrets-Management/Sonarr/Shared/SONARR_DRIVER
SONARR_GELF_ADDRESS=op://Secrets-Management/Sonarr/Shared/SONARR_GELF_ADDRESS
SONARR_TZ=op://Secrets-Management/Sonarr/Shared/SONARR_TZ
SONARR_IMAGE=op://Secrets-Management/Sonarr/Shared/SONARR_IMAGE
SONARR_SHARED=op://Secrets-Management/NAS/Volume_Mappings/SONARR_SHARED
TDARR_URL=op://Secrets-Management/Tdarr/TDARR_URL

# ------------
# Sonarr
SONARR_PUID=op://Secrets-Management/Sonarr/Sonarr/SONARR_PUID
SONARR_PGID=op://Secrets-Management/Sonarr/Sonarr/SONARR_PGID
SONARR_VOL_CONFIG=op://Secrets-Management/Sonarr/Sonarr/SONARR_VOL_CONFIG

SONARR_TDARR_DB_ID=op://Secrets-Management/Sonarr/Sonarr/SONARR_TDARR_DB_ID
SONARR_TDARR_TRANSLATE=op://Secrets-Management/Sonarr/Sonarr/SONARR_TDARR_TRANSLATE
# ------------
# Sonarr4K
SONARR4K_PUID=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_PUID
SONARR4K_PGID=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_PGID
SONARR4K_VOL_CONFIG=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_VOL_CONFIG

SONARR4K_DOCKER_MOD=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_DOCKER_MOD
SONARR4K_TP_ADDON=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_TP_ADDON

SONARR4K_TDARR_DB_ID=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_TDARR_DB_ID
SONARR4K_TDARR_TRANSLATE=op://Secrets-Management/Sonarr/Sonarr4K/SONARR4K_TDARR_TRANSLATE
```

</details>

## Wizarr

<details id="bkmrk-compose.yaml-----ver-2"><summary>compose.yaml</summary>

```yaml
---
version: "3.5"
services:
  wizarr:
    container_name: wizarr
    image: ${WIZARR_IMAGE}
    ports:
      - 5690:5690
    volumes:
      - ${WIZARR_VOL_DATABASE}:/data/database
    logging:
      driver: ${WIZARR_DRIVER}
      options:
        gelf-address: ${WIZARR_GELF_ADDRESS}
    restart: unless-stopped
```

</details><details id="bkmrk-.env-wizarr_image%3Dop"><summary>.env</summary>

```
WIZARR_IMAGE=op://Secrets-Management/Wizarr/WIZARR_IMAGE
WIZARR_VOL_DATABASE=op://Secrets-Management/Wizarr/WIZARR_VOL_DATABASE

WIZARR_DRIVER=op://Secrets-Management/Wizarr/WIZARR_DRIVER
WIZARR_GELF_ADDRESS=op://Secrets-Management/Wizarr/WIZARR_GELF_ADDRESS
```

</details>