# citadel-fwf-docker

Dockerized Standalone Server for Citadel: Forged With Fire


## Requirements

- docker
- docker-compose (optional)
- >5GB of RAM


## Running - Docker

This docker image is fairly straightforward to run

```
$ docker run -d --name citadel akshmakov/citadel-fwf:latest
```

The server should start, you can connect directly to the docker container from your local PC


To expose this server to the outside world we need to expose ports, which by default, or at least my interpretation of the defaults
is port 27015 and port 7777, if you are using different ports substitute them here.

```
$ docker run -d --name citadel -p "27015:27015" -p "7777:7777" akshmakov/citadel-fwf:latest
```

You may wish to restrict this port to only be available on certain network interfaces, you can do so by modifying the `-p` flag, assuming that you wish to only allow access via ip 192.168.1.100 - `-p "192.168.1.100:7777:7777"

### Configuration 

To use a locally tailored configuration directory:

```
$ docker run -d --name citadel -v "/PATH/TO/CONFIGS:/opt/citadel/Citadel/Saved/Config/LinuxServer" akshmakov/citadel-fwf:latest
```

To persist save files, use the same as above but add a ` -v "/PATH/TO/SAVES:/opt/citadel/Citadel/Saved/Config/LinuxServer" `

To login to the image to examine the files

```
$ docker exec -it citadel bash
```

To do so as root (allowing experimentation for custom images)

```
$ docker exec -it --user root citadel bash
```

Alternatively, one can "hardcode" the config files by extending the image. We assume that you have preprepared configs in a folder on the host computer. Note that the default server configs are empty in the image

```Dockerfile
FROM akshmakov/citadel-fwf:latest

## Adds everything from ./configs/ 
ADD configs /opt/citadel/Citadel/Saved/Config/LinuxServer/
```

build and run the custom image

```
$ docker build --tag local/citadel-fwf .
$ docker run -d --name citadel local/citadel-fwf
```



## Running Docker-compose

Docker compose can be used, running multiple instances will require a beefy machine. Configuration can be easier to manage


``` docker-compose.yml

version: '2'

services:
  citadel:
    image: akshmakov/citadel-fwf:latest
    ports:
      - "27015:27015"
      - "7777:7777"
    volumes:
      # Copy individual files read only
      - "/srv/citadel/configs/Game.ini:/opt/citadel/Citadel/Saved/Config/LinuxServer/Game.ini:ro"
      # Persist entire save folder
      - "/srv/citadel/saves:/opt/citadel/Citadel/Saved/SaveGames"
      
```

Start this quite simply

```
$ docker-compose up -d
```

To log in to a shell

```
$ docker-compose exec -it citadel bash
```

### Container Information

This container is a refactoring of a few public tutorials on setting up a citadel server standalone.

There is a local user steamserver to execute the application. SteamCMD and Citadel server are installed
into `/opt/steamcmd` and `/opt/citadel`

