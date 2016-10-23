# Docker Swarm


## Demo with local development

Docker Swarm makes clustering your services much easier.
So if you wanted to have an easy way to reverse proxy, load balance,
and scale up you can use docker swarm to make it easier.

Terminology:
* `agent` - nodes (usually host machines) that contain containers
* `manager` (master) - a machine that supervises the agent machines/containers


# Educational demo on a local virtualmachine
# Step 1. create a discovery machine called local (used only in dev and staging not production)
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
