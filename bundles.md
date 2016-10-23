# Docker Bundles

Bundles allows you to version control your containers that you ship to staging and production.
I'm not 100% sure what the difference is with an image and a bundle. 
I think bundles are snapshots of whole services that are bundled and that
you can ship to staging/production.

### Before you start

You must make changes to your `docker-compose.yml` file in order for it
to work with docker bundles:
* remove volumes
* add this to each of your service that doesn't have an image defined
```
image: pebreo/djangodocker_web:latest
```

### Creating bundles
```
dc bundle

cat <myservice>.dab

# if you haven't created a bundle before
dc bundle --push-images
```


# EXAMPLE DOCKER-COMPOSE.YML COMPATIBLE WITH BUNDLES
```yaml
version: "2"

services:
  web:
    image: pebreo/djangodocker_web:latest
    restart: always
    build: ./web
    expose:
      - "8000"
    networks:
      - backend
    volumes:
      - ./web:/usr/src/app # enable refresh when code is changed
      #- /usr/src/app
      - django-static:/usr/src/app/collectstatic
    env_file: .env
    environment:
      DEBUG: 'true'
    command: /usr/local/bin/gunicorn docker_django.wsgi:application -w 2 -b :8000

  nginx:
    image: pebreo/djangodocker_nginx:latest
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