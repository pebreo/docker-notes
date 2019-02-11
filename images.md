# Docker images


PULLING AND UPDATING IMAGES
--------------------------
#### Pulling and updating an image
```
d pull pebreo/test1

d run -it ubuntu bash

mkdir data ; cd data ; touch foo.txt 
exit

# the container id is in the bash terminal of the container
d commit <containerid> pebreo/test1
d push <tag>

d run -it pebreo/test1 bash
```
#### Image digests
Image digests are SHA-based strings that are assigned when you use `v2` or `latest`
keywords in your image tags.
```
d run ubuntu bash
mkdir data ; cd data ; touch foo.txt
exit
d commit <containerid> pebreo/test1:v3
d images --digests |head
```
#### Pull an image based on digest value
```
d pull pebreo/test1@sha256:f0ef578ebd7e92c1585ca63fa8a2feacc390555e719656a490183d37f892b46d
```

CREATE/UPDATE A NEW IMAGE FROM A DOCKERFILE
-----------------------------------
#### Make a Dockerfile
```
FROM pebreo/djangodocker_web_dev:latest

RUN npm install -g -y gulp-webpack
```
Now build the image, tag, and push it to Docker registry.
```
cd web

d build -t pebreo/devimage:latest -f base-dev-image-Dockerfile `pwd`
d push pebreo/devimage:latest
d images
```


RUN vs. EXEC vs. ATTACH 
----------------------
* Run means you start a container based on an image.
* Attach means attach to a running process.
* Exec means you run a new thing in an already running container.

#### d run - start a container based on an image
```
d run --rm busybox /bin/echo 'hello world'
d run -it ubuntu bash
```
#### d exec  - run new things in an already running container
```
d run -it ubuntu bash
CTRL+P then CTRL+Q
CTRL+R, CTRL+X (in dvorak mode)
d ps
d exec <containerid> /bin/echo 'hello'
d exec -it <containerid> bash
```
#### update a container and put in the background
```
d exec <containerid> npm install -y 
```

#### d attach - attach to a running process
```
d run -it ubuntu bash
# detach from a container
CTRL+R, CTRL+X (dvorak)  - CTRL+P, CTRL+Q (qwerty)
d attach <containerid>
```


#### run an interactive container
```
d run -it ubuntu bash
# -t assign pseudo-tty inside the container
# -i make interactive connection 
# ubuntu - the image
# bash - the command to run
```

PORTS
------
#### Run a container with port connection to host
```
d run -d -P training/webapp python app.py
# -d - run in background
# -P - assign a random port to host

d run -d -p 80:5000 training/webapp python app.py
# -p - assign container port 5000 to host port 80
```

EXAMINING A CONTAINER
----------------------
```
d port <containerid> <exposedport> # check what port the exposed port is mapped to
d top <containerid>  # examine the running process inside the container
```
#### Stopping, Starting, Removing container
```
d stop <containerid>
d start <containerid>
d rm <containerid>
```

START A CONTAINER IN THE BACKGROUND
------------------------------------
```
d run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
d ps
d logs <containerid>
d attach <containerid>

# -d - run container in background (daemonize it)
# ubuntu - the image the container will be based on
# /bin/sh -c etc  - run a specific command
```








