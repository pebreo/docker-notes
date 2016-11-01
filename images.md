# Docker images


#### Pulling and updating images
```
d pull

d run -it ubuntu bash
mkdir data ; cd data ; touch foo.txt 
exit
# the container id is in the bash terminal of the container
d commit <containerid> <tag>
d push <tag>

d run -it pebreo/test1 bash
```

#### Make an image based on a running container
```
d image


d build -t pebreo/myimage:mytag  -f myDockerfile `pwd`


d run -p -d busybox mkdir baz ; cat 'hello' > /baz/foo.txt
d exec -d <containerid> yum apt
d tag <containerid>  pebreo/myimage:mytag
d push pebreo/myimage:mytag
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
d exec containerid npm install -y 
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
$ cd web
$ d build -t pebreo/djangodocker_web_dev:latest -f update-Dockerfile `pwd`
$ d push pebreo/djangodocker_web_dev:latest
$ d images
```



