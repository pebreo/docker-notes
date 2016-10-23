# Docker Machine

```bash

dma create -d virtualbox dev1

$ export DO_TOKEN="abcdefghijklmnopqrstuvwxyz"

$ docker-machine create \
-d digitalocean \
--digitalocean-access-token=${DO_TOKEN} \
serve1


dma ip dev1

dma stop dev1

dma start dev1

dma ssh dev1

```

## If you can't connect to the machine
```
dma regenerate-certs dev1
```
