
# Docker Compose

development workflow
---------------------
```yaml

dc up -d

dc run --rm web /usr/local/bin/python manage.py collectstatic --noinput
dc run --rm web /usr/local/bin/python manage.py migrate
dc run --rm web /usr/local/bin/python manage.py createsuperuser


# if you just need to restart one microservice/container
dc stop web
dc rm -f web
dc up -d web

```

IMAGE VS BUILD (IN VERSION 2-TYPE COMPOSE FILES)
-----------------------------------------------
The following only applies to version 2-type compose files.
In version 1-type compose files, you can't have `image` and `build` both defined
for a service.

Let's say you define your service with `build` and `image` declarations:
```yaml
version: "2"
services:
  web:
    image: pebreo/myapp:latest
    build: ./web  
```
In version 2-type compose files, ff you have both the `build` and `image` defined, then the the built image will be tagged with `pebreo/myapp:latest`, after you run `dc build`.

If you only had `build` defined then the image will just be name
`<currentdir>_<myservicename>`

If only have `image` defined, then it will only use the image
to spin up a container. You do this when you want to deploy to staging/prod.
You would have to make sure that your `Dockerfile` copies your app
code to the image like this:
```
FROM python3.5
RUN mkdir -p /usr/src/app
COPY code/ /usr/src/app
WORKDIR /usr/src/app
```
And to push to staging/prod you will have to build, tag, and push.
##### Build and tag
```
d build -t pebreo/myapp:staging-0.1 -f staging-Dockerfile `pwd`
d push pebreo/myapp:staging-0.1
```
##### Finally, update the image version in your staging docker-compose file e.g. `staging.yml`
```yaml
version: "2"
services:
  web:  
    image: pebreo/myapp:staging-0.1
```


**Version 1-type** `docker-compose.yml` file 
---------------------------------------
A couple of notes about version 1-type compose files:
* In order to connect services, you need to use `links` declaration.
* The `expose` declaration, exposes that containers port to other containers.
* The `ports` declaration, exposes the container ports to the host. For example,
the declaration `"80:80"` means `"host_port:container_port"`.
* You cannot use `image` and `build` at the same time.
```yaml

web:
  restart: always
  build: ./web
  expose:
    - "8000"
  links:   
    - redis:redis
  volumes:
    - ./web:/usr/src/app
  env_file: .env
  environment:
    DEBUG : 'true'
  command: /usr/local/bin/gunicorn docker_django.wsgi:application -w 2 -b :8000

nginx:
  container_name: nginxcont
  restart: always
  build: ./nginx/
  ports:
    - "80:80"
  volumes:
    - /www/static
  volumes_from:
    - web
  links:
    - web:web

redis:
  restart: always
  image: redis:latest
  ports:
    - "6379:6379"
```

**Version 2-type** `docker-compose.yml` file 
---------------------------------------
A couple notes about version 2-type compose files:
* All services are in the same `networks` by default.
* If you want to connect services then you can use the `networks` label.
For example, if you wanted the `postgres` service to only connect to `web` 
then you can make a separate network for it called `db_backend`.
* You can connect to other containers by using the service name of the container you
want to connect to. For example, from the `web` container you can access `nginx`
by doing something like: `nc -w 1 -z nginx 8000`
* The `volumes` declaration means you can define named containers. Those are
useful because named volumes persiste even after you delete containers that connect to it.
```yaml
version: "2"

services:
  web:
    restart: always
    build: 
      context: ./web
      dockerfile: base-django1.8-dev-Dockerfile    
    expose:
      - "8000"
    networks:
      - backend
      - db_backend # this is just for demo purpose
    volumes:
      # during development we mount to local directory to allow livereload when code is changed
      - ./web:/usr/src/app
      # a named volume persists even after we delete the container that attaches to it
      - django-static:/usr/src/app/collectstatic 
    env_file: .env
    environment:
      DEBUG: 'true'
    command: /usr/local/bin/gunicorn docker_django.wsgi:application -w 2 -b :8000

  nginx:
    restart: always
    build: 
      context: ./nginx
      dockerfile: base-nginx-dev-Dockerfile    
    ports:
      - "80:80"
    volumes:
      - /www/static
    volumes_from:
      - web
    networks:
      - backend

  postgres:
    restart: always
    image: postgres:latest
    volumes:
      - pgdata:/var/lib/postgresql/data/
    networks:
      - db_backend # this is just for demo purpose

  redis:
    restart: always
    image: redis:latest
    networks:
      - backend
      

volumes:
  django-static:
      driver: local
  pgdata: {}

networks:
  backend: {}
  db_backend: {}
```

volumes
--------
creating named volumes allows other containers can share it. also, named volumes have a name so that you can just do a 'docker volume ls |grep volumename' and then do a 'docker volume inspect' which will then tell you where on the host the volume is mounted (usually `/mnt/docker/blah`) 

```yaml

services:
   web

volumes:
   myvolume:
     driver: local # this is the default driver
```

## auto reload

If you want to autoreload you should use the syntax:

```yaml
  volumes:
      - ./web:/usr/src/app 
```

You can't do this in production, so you'll have to take that line out and substitute it with the commented
out line.


