
# Docker Compose

## development workflow

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

## Typical docker-compose.yml
```yaml

version: "2"

services:
  web:
    restart: always
    build: ./web
    expose:
      - "8000"
    networks:
      - backend
    volumes:
      - ./web:/usr/src/app # during development we mount to local directory to allow livereload when code is changed
      #- /usr/src/app # during production, we use this instead of above line
      - django-static:/usr/src/app/collectstatic # a named volume 
    env_file: .env
    environment:
      DEBUG: 'true'
    command: /usr/local/bin/gunicorn docker_django.wsgi:application -w 2 -b :8000

  nginx:
    restart: always
    build: ./nginx/
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
      - backend

  redis:
    restart: always
    image: redis:latest
    networks:
      - backend

volumes:
  django-static:
      driver: local
  pgdata:

networks:
  backend:
```

## volumes

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


