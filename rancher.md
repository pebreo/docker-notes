# Rancher

Rancher let's your manager clusters of machines. 
With Rancher, it is easy to monitor and get statitics on each of your machines.

### Terminology
* Environments - e.g. staging, production
* Hosts - a server (e.g. DigitalOcean droplet or AWS EC2 instance)
* Stacks - a logical collection of services (e.g. frontend, backend)
* Service - a collection of containers (which you can define in docker-compose.yml)

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




### docker-compose.yml 
```yaml
version: "2"

services:  
  api:
    container_name: api
    image: jdelight/films_api
    build: ./api
    networks:
      - backend
    ports:
      - "8000:8000"
    volumes:
      - ./api:/var/www/app
    env_file: .env
    tty: true
    depends_on:
      - db
    command: /var/www/app/start.sh

  db:
    container_name: db
    image: jdelight/films_db
    build: ./db
    networks:
      - backend
    env_file: .env
    volumes:
      - "dbdata:/var/lib/postgresql/data"

volumes:  
  dbdata:

networks:  
  backend:
```

Sources:
http://jamesdacosta.com/getting-started-with-django-docker-rancher-part-2/


