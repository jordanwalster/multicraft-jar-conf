# Multicraft Jar Configurations

This repository contains the Multicraft jar configuration files that are used in conjunction with the [java-multi-version](https://git.jrdn.dev/jordanwalster/-/packages/container/java-multi-version/latest) docker image. 

This allows Multicraft to utilise the built in docker feature whilst maintaining the ability to run different versions of Minecraft, since the `multicraft.conf` forces the selection of a single jdk image.

## How to use

The configurations from this repo can be used with a bare metal installation of Multicraft, but my preferred method is using Docker-in-Docker, allowing the entire minecraft stack to be containerised.

1. Grab your key from Multicraft and store it in the root directory.

2. Run Multicraft using the [multicraft-docker](https://git.jrdn.dev/jordanwalster/-/packages/container/multicraft-docker/latest) image and the accompanying `docker-compose.yml` & `.env` files.

3. Navigate to the `install.php` file and complete the setup matching the values to the `.env` file.  

4. Once setup is complete, navigate to Settings > Update Minecraft > Add or Remove Files
    - In the JAR Filename, File URL and Conf URL fields, enter the chosen file: e.g.
      - JAR Filename: `paper-1.21.4.jar`
      - File URL: `https://mc-assets.jrdn.dev/jar/paper-1.21.4.jar`
      - Conf URL: `https://mc-assets.jrdn.dev/conf/paper-1.21.4.jar.conf`

All configuration files are in the <strong>/conf</strong> path, matching their corresponding `.jar` files in <strong>/jar</strong>, with the same name plus a `.conf` extension.


### Docker compose and .env file

#### docker-compose.yml

```
services:
  multicraft:
    image: git.jrdn.dev/jordanwalster/multicraft-docker:latest
    ports:
      - 80:80
      - target: 21
        published: 21
        mode: host
    volumes:
      - ${BASE_DIR}/web-data:/var/www/html
      - ${BASE_DIR}/multicraft/jar:/multicraft/jar
      - ${BASE_DIR}/multicraft/servers:/multicraft/servers
      - ${BASE_DIR}/multicraft/templates:/multicraft/templates
      - ${BASE_DIR}/multicraft.key:/multicraft/multicraft.key
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - BASE_DIR=${BASE_DIR}
      - SERVER_MEMORY=${SERVER_MEMORY}
      - JDK_IMAGE=git.jrdn.dev/jordanwalster/java-multi-version:latest
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    networks:
      - multicraft
    depends_on:
      - db
  db:
    image: mariadb
    volumes:
      - ${BASE_DIR}/db:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
    networks:
      - multicraft

networks:
  multicraft:
    driver: bridge
```

#### .env

```
BASE_DIR=/path/to/data
SERVER_MEMORY=2048
MYSQL_USER=multicraft
MYSQL_DATABASE=multicraft
MYSQL_PASSWORD=changeme
MYSQL_ROOT_PASSWORD=changeme
```
