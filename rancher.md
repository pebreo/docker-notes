# Rancher

Rancher let's your manager clusters of machines. 
With Rancher, it is easy to monitor and get statitics on each of your machines.

### Terminology
* Environments - e.g. staging, production
* Hosts - a server (e.g. DigitalOcean droplet or AWS EC2 instance)
* Stacks - a logical collection of services (e.g. frontend, backend)
* Service - one or more containers of the same image

### SETUP FOR DIGITALOCEAN


##### Provision a `manager` machine
```
export DO_TOKEN="abc123"

# it won't start unless it is 1gb machine
docker-machine create \
  -d digitalocean \
  --digitalocean-access-token ${DO_TOKEN} \
  --digitalocean-size "1gb" \
  manager1 
```
##### Install Rancher

```
deval manager
docker run -d --restart=unless-stopped -p 8080:8080 rancher/server

```
##### Wait a couple minutes for the server : `.... Startup Succeeded, Listening on port...`
```
d logs -f <containerid>
dma ip manager
goto: http://<SERVER_IP>:8080
```
##### Add nodes (agents,hosts)
```
https://www.digitalocean.com/community/tutorials/how-to-manage-your-multi-node-deployments-with-rancher-and-docker-machine-on-ubuntu-14-04
```

### A simple Rancher Deployment
This deployment will be a basic deployment using:
* The rancher server will have a agent server on it too
* The login is local (authentication using login/password)
* No load balancing

##### Prepare your docker-compose.yml
* remove nginx container
* should have environ setup
* should wait for postgres database using special start.sh in web service
* the stack will not have a load balancer. it will only have web, postgres, 

##### Push your images to docker hub (or docker registry)
* dc bundle --push-images
* or, d push pebreo/myimage:mytag

##### Add a service (container)
* set the image
* set the destination service
* set environment vars

##### 

### docker-compose.yml 
```yaml
api:  
  container_name: api
  image: jdelight/films_api:0.1.1
  ports:
    - "8000:8000"
  tty: true
  env_file: .env
  command: /var/www/app/start.sh

db:  
  container_name: db
  image: jdelight/films_db:0.1.1
  env_file: .env
  volumes:
    - "dbdata:/var/lib/postgresql/data"

lb:  
  image: rancher/load-balancer-service
  ports:
    - 80:8000
  links:
    - api:api
```

Sources:
http://docs.rancher.com/rancher/v1.2/en/quick-start-guide/
http://jamesdacosta.com/getting-started-with-django-docker-rancher-part-2/


