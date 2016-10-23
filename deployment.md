# Deployment


## Development cycle

Before we start let's talk about how to develop locally


## Deployment

### 1.Make a swarm (using machines and docker swarm)

#### Create the swarm token using a temporary machine
```
export DO_TOKEN="abc123"

docker-machine create \
  -d digitalocean \
  --digitalocean-access-token ${DO_TOKEN} \
  --digitalocean-region "lon1" \
  --digitalocean-size "512mb" \
  swarm-1 \

deval swarm-1
d run swarm create

export SWARM_TOKEN="xxz"

docker-machine rm swarm1
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
#### (optional) add constraints
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

make sure in dockerhub that your web app is private

# if you haven't created a bundle before
dc bundle --push-images
```

### 3.Deploy bundle to swarm 
```
d node ls
d deploy dockerizingdjango.dab # create a stack from a bundle
d service ls
d service inspect <containerid/name>
```

### 4. (optional) Drain a node (agent) for update
```
docker node inspect self --pretty
docker node inspect agent01 --pretty
docker node update --availability drain agent1
```

### 4. (optional. for local development only) Scale a service (without swarm)
###### before the next command you should remove non-default ports declaration e.g. ports ["50:50"]
```yaml
services:
    myservice:
        build: /code
        image: pebreo/myservice
# notice there is no ports declaration
```
###### run the command
```
dc scale myservice=2
```

source: https://42notes.wordpress.com/2015/04/15/create-manage-a-docker-swarm-cluster-on-digital/