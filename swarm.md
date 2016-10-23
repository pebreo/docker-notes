# Docker Swarm


## Demo with local development

Docker Swarm makes clustering your services much easier.
So if you wanted to have an easy way to reverse proxy, load balance,
and scale up you can use docker swarm to make it easier.

Terminology:
* `agent` - nodes (usually host machines) that contain containers
* `manager` (master) - a machine that supervises the agent machines/containers
* A docker service is a set of containers that are launched on your nodes and a certain
number of contaire are kept running at all times. If one of the container dies it is replaced automatically.
* You can use constraints and labels to do some very basic scheduling of containers as well
as constraining resources like memory. For example
```
docker service create \
 -name frontend \
 -replicas 5 \
 -network my-network \
 --consraint engine.labels.cloud==aws
 --constraint node.role==manager \
 -p 80:80/tcp nginx:latest
```
## Educational demo on a local virtualmachine
### Step 1. create a discovery machine called local (used only in dev and staging not production)
```
dma create -d virtualbox local
```
### Step 2. Generate swarm token
```
d run swarm create
```
### Step 3. Create manager and agent hosts locally on VM
```
# the master
 docker-machine create \
         -d virtualbox \
         --swarm \
         --swarm-master \
         --swarm-discovery token://<TOKEN-FROM-ABOVE> \
         swarm-master

# the agents
  docker-machine create \
     -d virtualbox \
     --swarm \
     --swarm-discovery token://<TOKEN-FROM-ABOVE> \
     swarm-agent-01

# the agents
  docker-machine create \
     -d virtualbox \
     --swarm \
     --swarm-discovery token://<TOKEN-FROM-ABOVE> \
     swarm-agent-02
```
### Step 4. Control the manager and run a test command
```
eval $(docker-machine env --swarm swarm-master)
$ d info
$ d ps -a
$ d run hello-world
$ d ps -a
```

source: https://docs.docker.com/swarm/install-w-machine/


## SWARM ON DIGITAL OCEAN

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

#### Create swarm master (manager)
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

#### node2 (agent)
docker-machine create \
-d digitalocean \
--digitalocean-access-token ${DO_TOKEN} \
--digitalocean-region "lon1" \
--digitalocean-size "512mb" \
--swarm  \
--swarm-discovery token://${SWARM_TOKEN} \
agent2

#### cluster check
docker-machine ls


#### attach dma to master (manager)
eval $(docker-machine env --swarm swarm-master)
d info

#### add constraints
# allow on 400 MB for nginx
docker run --name <containername> -d -P -m 400M nginx
d ps
d info
```

source: https://42notes.wordpress.com/2015/04/15/create-manage-a-docker-swarm-cluster-on-digital/