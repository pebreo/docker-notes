# Deployment


## Development cycle

Before we start let's talk about how to develop locally


## Deployment

### 1.Make a swarm (using machines and docker swarm)

####
```
export DO_TOKEN="abc123"

docker-machine create \
  -d digitalocean \
  --digitalocean-access-token ${DO_TOKEN} \
  --digitalocean-region "lon1" \
  --digitalocean-size "512mb" \
  temp-swarm \

deval temp-swarm
d run swarm create

export SWARM_TOKEN="xxz"

docker-machine rm temp-swarm
```
#### Create swarm master (manager)
```
docker-machine create \
  -d digitalocean \
  --digitalocean-access-token ${DO_TOKEN} \
  --digitalocean-region "lon1" \
  --digitalocean-size "512mb" \
  --swarm --swarm-master \
  --swarm-discovery token://${SWARM_TOKEN} \
  manager1

#### Add node1 (agent) to cluster 
docker-machine create \
  -d digitalocean \
  --digitalocean-access-token ${DO_TOKEN} \
  --digitalocean-region "lon1" \
  --digitalocean-size "512mb" \
  --swarm \
  --swarm-discovery token://${SWARM_TOKEN} \
  agent1
```
#### node2 (agent)
```
docker-machine create \
-d digitalocean \
--digitalocean-access-token ${DO_TOKEN} \
--digitalocean-region "lon1" \
--digitalocean-size "512mb" \
--swarm  \
--swarm-discovery token://${SWARM_TOKEN} \
agent2
```
#### cluster check
```
docker-machine ls
```

#### attach dma to master (manager)
```
eval $(docker-machine env --swarm swarm-master)
d info
```
#### add constraints
```
# allow on 400 MB for nginx
docker run --name <containername> -d -P -m 400M nginx
d ps
d info
```

### 2.Make a bundle
```
dc bundle

cat <myservice>.dab

# if you haven't created a bundle before
dc bundle --push-images
```

### 3.Deploy bundle to swarm 
```
d node ls
d deploy dockerizingdjango.dab
d service ls
d service inspect <containerid/name>
```