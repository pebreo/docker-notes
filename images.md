# Docker images


#### Pulling and updating images
```
d pull <image tag>

d run -it ubuntu bash

mkdir data ; cd data ; touch foo.txt 
exit

# the container id is in the bash terminal of the container
d commit <containerid> pebreo/test1
d push <tag>

d run -it pebreo/test1 bash
```


run vs exec vs attach 
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
CTRL+R+X
d ps
d exec <containerid> /bin/echo 'hello'
```
#### d attach - attach to a running process
d run -it ubuntu bash
CTRL+R+X
d attach <containerid>
```

Run means you create a container based on an image.
#### run from an image
````
d run busybox /bin/echo 'hello world'
```

#### run and delete container
```
d run --rm busybox /bin/echo 'hello world'
```

#### run an interactive container
```
d run -it ubuntu bash
# -t assign pseudo-tty inside the container
# -i make interactive connection 
# ubuntu - the image
# bash - the command to run
```

#### detach from a container without exiting shell
CTRL+R+X (DVORAK)
CTRL+P+Q (QWERTY)

#### attach into a container
```
d attach <containerid>
```

#### Run a container with port connection to host
```
d run -d -P training/webapp python app.py
# -d - run in background
# -P - assign a random port to host

d run -d -p 80:5000 training/webapp python app.py
# -p - assign container port 5000 to host port 80
```

#### Examine the running container
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

#### start a container in background
```
d run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
d ps
d logs <containerid>
d attach <containerid>

# -d - run container in background (daemonize it)
# ubuntu - the image the container will be based on
# /bin/sh -c etc  - run a specific command
```


#### update a container and put in the background
```
d exec <containerid> npm install -y 
```

MAKE A NEW IMAGE FROM A DOCKERFILE
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



