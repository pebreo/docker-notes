### Terminology
* Environments - e.g. staging, production
* Hosts - a server (e.g. DigitalOcean droplet or AWS EC2 instance)
* Stacks - a logical collection of services (e.g. frontend, backend)
* Service - a collection of containers (which you can define in docker-compose.yml)

### SETUP FOR DIGITALOCEAN


##### Provision a `manager` machine
```
dma create -d digitalocean \
```
##### Install Rancher

```
deval manager
sudo docker run -d --restart=always -p 8080:8080 rancher/server  
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


