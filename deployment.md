# Deployment


## Development cycle

Before we start let's talk about how to develop locally


## Deployment

#### 1.Make a swarm (using machines and docker swarm)

#### 2.Make a bundle
```
dc bundle

cat <myservice>.dab

# if you haven't created a bundle before
dc bundle --push-images
```
#### 3.Deploy bundle to swarm 
```
d node ls
d deploy dockerizingdjango.dab
d service ls
d service inspect <containerid/name>
```