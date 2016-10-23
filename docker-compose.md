
# Docker Compose


## docker-compose.yml
```

```

## volumes

creating named volumes allows other containers can share it. also, named volumes have a name so that you can just do a 'docker volume ls |grep volumename' and then do a 'docker volume inspect' which will then tell you where on the host the volume is mounted (usually `/mnt/docker/blah`) 

```yaml

services:
   web

volumes:
   myvolume:
     driver: local # this is the default driver
```