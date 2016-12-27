# Rancher

Rancher let's your manager clusters of machines. 
With Rancher, it is easy to monitor and get statitics on each of your machines.

### Terminology
* Environments - e.g. staging, production
* Hosts - a server (e.g. DigitalOcean droplet or AWS EC2 instance)
* Stacks - a logical collection of services (e.g. frontend, backend)
* Service - one or more containers of the same image

In Rancher, you can deploy your app in two ways:
1) Add the containers individual container images separately
   This allows you to control what containers are in what hosts
   and you can connect them using "Links" optional when you create a container
2) Add whole stacks using rancher-compose. I have not figured out how to constrain
   which containers go to which hosts with this method though.

SIMPLE RANCHER DEPLOYMENT USING DIGITAL OCEAN
---------------------------------------------
This deployment will be a basic deployment using:
* The rancher server will have a agent server on it too
* The login is local (authentication using login/password)
* No load balancing

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

##### Goto Admin and setup local authentication
* Goto admin -> Access Control 
* select local authentication

#### Add a host to the manager for testing purposes
* `dma ssh manager1`


##### Add nodes (agents,hosts)
```
https://www.digitalocean.com/community/tutorials/how-to-manage-your-multi-node-deployments-with-rancher-and-docker-machine-on-ubuntu-14-04
```

DEPLOYMENT REMINDERS
---------------------------

##### Prepare your docker-compose.yml
* remove nginx container
* should have environ setup
* should wait for postgres database using special start.sh in web service
* the stack will not have a load balancer. it will only have web, postgres, 

##### In Rancher, add a service (container)
* set the image
* set the destination service
* set environment vars

##### Add the command in Rancher
For example:
```
  environment:
    DEBUG: 'true'
  command: /usr/local/bin/python /usr/src/app/app.py 
```

### docker-compose.yml 
```yaml
version: '2'
services:
  web:
    image: pebreo/dockerflask_web_staging:0.1.8
    depends_on:
      - redis
    stdin_open: true
    tty: true
    ports:
    - 80:5000/tcp
    env_file: .env
    environment:
      DEBUG: 'false'
    command: /usr/local/bin/gunicorn -w 2 -b 0.0.0.0:5000 app:app

  redis:
    restart: always
    image: redis:latest
    ports:
      - "6379:6379"
    volumes:
      - redisdata:/data

volumes:
  redisdata: {}
```
### rancher-compose.yml
```
version: '2'
services:
  web:
    scale: 1
    start_on_create: true
  redis:
    scale: 1
    start_on_create: true
```

INTERMEDIATE RANCHER DEPLOYMENT USING COMPOSE AND UPGRADE
----------------------------------------------------------
The overview of steps are:
* Provision a Rancher server using Digital Ocean
* Build the app images using `dc-build.py`
* Push the app images using `dc-build.py <ver> push`
* Install `rancher-compose` binary to `/usr/local/bin`
* Create a `rancher-compose.yml` file
* Deploy using `rancher-compose` command


#### Provision Rancher server
```
export DO_TOKEN="abc123"

# it won't start unless it is 1gb machine
docker-machine create \
  -d digitalocean \
  --digitalocean-access-token ${DO_TOKEN} \
  --digitalocean-size "1gb" \
  manager1 

deval manager
docker run -d --restart=unless-stopped -p 8080:8080 rancher/server

# ... Wait a couple minutes for the server : `.... Startup Succeeded, Listening on port...`

d logs -f <containerid>
dma ip manager
goto: http://<SERVER_IP>:8080
```

#### Install `rancher-compose` binary to `/usr/local/bin`
* Goto your Rancher manager UI
* On the bottom-right click on "Download Rancher CLI"
* Save to Download and untar
* Goto terminal and move the file to `/usr/local/bin`


#### Build and push app images using `dc-build.py`
```
python dc-build.py 0.1.5
python dc-build.py 0.1.5 push
optional :  export RANCHER_CLIENT_DEBUG=true
```


#### Create a `rancher-compose.yml` file
```
version: '2'
services:
  web:
    scale: 1
    start_on_create: true
  redis:
    scale: 1
    start_on_create: true
```

#### Deploying using `rancher-compose` command

Create the API keys first by going to API -> Advanced Options 
then "Add Environment API Key"
You will need the:
 * ACCESS KEY (i.e. username)
 * SECRET KEY (i.e. password)
```
rancher-compose -f staging-0.1.7.yml --url http://hostip:8080/ \
--access-key abc \
--secret-key xyz \
-p mystack up -d

```

#### Upgrading

When upgrading, you should increment the version number when 
you call `dc-build.py`.
Then you should use the `upgrade` paramater in the `rancher-compose` command
```
python dc-build.py 0.1.6
python dc-build.py 0.1.6 push

rancher-compose -f staging-0.1.8.yml --url http://hostip:8080/ \
--access-key abc \
--secret-key xyz \
-p mystack up --upgrade -d web


optional : rancher-compose --debug up -d 
optional : rancher-compose up myservec1 myservice2 -d
```

MAKING UPDATES TO YOUR APP USING RANCHER COMPOSE
------------------------------------------------
```
rancher-compose -f staging-0.1.2.yml up --upgrade myservice
```


Sources:
http://docs.rancher.com/rancher/v1.2/en/quick-start-guide/
http://jamesdacosta.com/getting-started-with-django-docker-rancher-part-2/


