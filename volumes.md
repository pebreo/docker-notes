# Docker Volumes

## Hands-on tutorial
### Part 1: Mounting in 1 container
#### create container1 with a file inside a volume
```
d run -it -v /data --name container1 busybox 
cd data
touch file1.txt
exit
```
#### get the mount path on the host machine
```
# copy the source path
d inspect container1  # notice the "Mounts" source path

```
#### goto host machine and look at the created file 
```
dma ssh dev1
sudo 
cd <the source path>
```
#### see if the volume is still there
```
d restart container1
d attach container1
```

### Part 2: Mounting 2 containers and using `volumes-from`
#### mount the local directory to the container directory called `/datavol`
```
d run -it --name container3 -v `pwd`:/datavol busybox
```
##### share data from one container to another
#####  and persist data from a non-persistant container
```
# first, create the data volume container

d run -it --name datacontainer1 -v /data busybox
CTRL+P+Q - exit without stopping container
CTRL+R+X on mac dvorak
```
#### check the volume if it persisted
```
# run the command remotely and give the output
d exec container1 ls /data

# second, create another container to mount the volume from datacontainer1

d run -it --volumes-from datacontainer1 --name datacontainer2 busybox
 
ls 
```

## Backing data from a data volume container


#### create a data volume container
```
d create -v /dbdata --name dbstore training/postgres /bin/true
```
##### backup the data volume container by creating a temporary container
```
# --rm automatically remove container when exited
# --volumes-from : attach a volume from another container
# -v : create a volume for this container, in this case, mount a local volume 
# image
# tar compress
d run --rm --volumes-from dbstore -v `pwd`:/backup ubuntu tar cvf /backup/backup.tar /dbdata
```