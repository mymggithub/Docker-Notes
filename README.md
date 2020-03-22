# Docker-Notes
Notes on docker written by Mark G

[video 1](https://www.youtube.com/watch?v=zJ6WbK9zFpI)

[video 2](https://www.youtube.com/watch?v=JprTjTViaEA) 

[Learn Docker in 12 Minutes](https://www.youtube.com/watch?v=YFl2mCHdv24) 

[dockerize python](https://runnable.com/docker/python/dockerize-your-python-application)

Installing docker has an easy way using the convient script with linux

#### Check the version

`sudo docker version`

### Containers

#### -List running containers

`docker ps`

#### -List all containers

`docker ps -a`

#### -Starts container first time, --name is not required, but its to name a container

`docker run --name run-name docker-username/file-name`

#### -Start a container again if stopped

`docker start [provide name or ID]`

#### -Stop a container

`docker stop [provide name or ID]`

#### Stop all containers

`docker ps -q -a | xargs docker stop`

#### -Force stop or Kill a container

`docker kill [provide name or ID]`

#### Kill all containers

`docker kill $(docker ps -q)`

#### -Remove a container

`docker rm  [provide name or ID]`

#### Remove all containers

`docker rm $(docker ps -q)`

#### Remove exited containers

`docker rm $(docker ps -a -f status=exited -q)`

If you get errors like I did, I recomend using.

`docker container prune`

If you get errors of.

```
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.35/containers/create: dial unix /var/run/docker.sock: connect: permission denied.See 'docker run --help'.
```
[The fix](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue) 

`sudo groupadd docker`

`sudo usermod -aG docker $USER`

`newgrp docker`

`docker run hello-world`


### Images

#### -List images

`docker images`

#### -Remove image (You need to stop and delete all dependent containers)

`docker rmi docker-username/file-name`

#### -Download and not run image

`docker pull docker-username/file-name`


### Other commands

#### -Run

`docker run docker-username/file-name`

#### -Execute a command

`docker exec [provide name or ID] cat /etc/hosts`

#### -Run Detach mode

`docker run -d --name run-name docker-username/file-name`

[To Detach using commands](https://stackoverflow.com/questions/19688314/how-do-you-attach-and-detach-from-dockers-process)

#### -Run and remove container after

`docker run --rm docker-username/file-name`

#### -Run Attach mode

`docker attach [provide name or ID]`

#### -Run an latest version

`docker run docker-username/file-name:latest`

#### -Run an older version :0.0

`docker run --name run-name docker-username/file-name:4.0`

#### -Run STDIN -i interactive mode -t terminal

`docker run -it --name run-name docker-username/file-name`

#### -Run port mapping out-container:in-container

`docker run -p 80:5000 --name run-name docker-username/file-name`

#### -Run volume mapping -v /folder/folder/folder out:in

`docker run -v /opt/datadir:/var/lib/mysql -name run-name docker-username/file-name`

another example

`docker run -v "$PWD":/usr/src/widget_app python:3 python my_script.py`

[more](https://thenewstack.io/docker-basics-how-to-share-data-between-a-docker-container-and-host/)

#### -Container details

`docker inspect run-name`

#### -Container logs
`docker logs run-name`

#### -Environment variables

`docker run -e APP_COLOR=blue --name run-name docker-username/file-name`

### How to create an image
Example for flask:
Filename: Dockerfile
```
From Ubuntu
RUN apt-get update
RUN apt-get python

RUN pip install flask
RUN pip install flask-mysql

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flash run
```

- From - is another image
- RUN - to install all dependencies
- COPY - Copy source code (Read more about [ADD vs COPY](https://nickjanetakis.com/blog/docker-tip-2-the-difference-between-copy-and-add-in-a-dockerile))
- ENTRYPOINT - Specify entrypoint, a command that will run when an image container

Other: [WORKDIR](https://stackoverflow.com/questions/51066146/what-is-the-point-of-workdir-on-dockerfile)

#### -Build the docker

`docker-username/my-custom-app` is really whatever you want to put for a tag, you account on docker hub account is not needed for local storage.

`docker build Dockerfile -t docker-username/my-custom-app` - this did not work for me like it said in the video

`docker build -t docker-username/my-custom-app .` - Recomended

#### -Push to public docker hub

`docker push docker-username/my-custom-app`

#### -You can see the history of the build using

`docker history docker-username/my-custom-app`

### ENTRYPOINT vs CMD

#### Commands 

`CMD command param1`

`CMD sleep 5`

`CMD bash`

or 

`CMD ["command", "param1"]`

`CMD ["sleep", "5"]`

`CMD ["bash"]`


#### In entrypoint you can append instructions at run without rewriting them

`ENTRYPOINT ["sleep"]`

`docker run my-custom-app-sleeper 10`

#### Its best to use both so it does not give an error with or without the needed params

`ENTRYPOINT ["sleep"]`

`CMD ["5"]`

`docker run my-custom-app-sleeper 10`


#### You can overwrite the original entrypoint

`docker run --entrypoint new-image-name my-custom-app-sleeper 10`


### Docker Network

#### Types of network bridge - none - host

Bridge is a privite internal network, all contanerss connect to this by defualt contaners and get and ip address, usualy in the 172.17.0.1 series and they can access each other with there ip, other then that you accesss is map ports.
usualy only creates on interinal network.


host takes out isolation, it uses the host network.

#### If you wish to make muliple isolation

`docker network create --driver bridge --subnet 182.18.0.0/16 custom-isolated-network`

#### To list all networks

`docker network ls`

Can get network info on a container with docker inspect command

### Docker file system
- `/var/lib/docker/`

  - aufs

  - containers

  - image

  - volumes

#### Create volumes in /var/lib/docker/volumes folder

`docker volume create data_volume`

#### Volume mounting

`docker run -v data_volume:/var/lib/mysql mysql`

#### Mounts and creates volumes

`docker run -v data_volume2:/var/lib/mysql mysql`

#### Bindmount volume

`docker run -v /data/mysql:/var/lib/mysql mysql`

#### Using -v is old, new and prefered way is --mount

`docker run --mount type=bind, source=/data/mysql, target=/var/lib/mysql mysql`

- Storage drivers

  - AUFS
    
  - ZFS
    
  - BTRFS
    
  - Device Mapper
    
  - Overlay
    
  - Overlay2
  
  
### Docker compose

`docker run -d --name=redis redis`

`docker run -d --name=db postgres:9.4`

`docker run -d --name=vote -p 5000:80 voting-app`

`docker run -d --name=result -p 5001:80 result-app`

`docker run -d --name=worker worker`

#### links add so they can network together, its also depricated

`docker run -d --name=redis redis`

`docker run -d --name=db postgres:9.4`

`docker run -d --name=vote -p 5000:80 --link redis:redis voting-app`

`docker run -d --name=result --link db:db -p 5001:80 result-app`

`docker run -d --name=worker  --link db:db --link redis:redis worker`

#### To create a docker compose
A docker compose file is made to package all images and containers needed to create and run a project.

Usualy writen in yml

Filename: docker-compose.yml

```
redis:
	image: redis
db:
	image: postgres:9.4
vote:
	image: voting-app
	ports:
		- 5000:80
	links:
		- redis
result:
	image: result-app
	ports:
		- 5001:80
	links:
		- db
worker:
	image: worker
	links:
		- redis
		- db
```

#### bring up the stack
`docker-compose up`

#### For a docker compose - build, we can add our own custome build image

replase

`image: voting-app`

with

`build: ./vote`

#### Docker compose verion 2

Docker compose verion 2 because version one had limitaions, links are not needed they can comunicate using service name. It also add a depends_on feature to saw what goes first

Filename: docker-compose.yml

```
version: 2
services:
	redis:
		image: redis
	db:
		image: postgres:9.4
	vote:
		image: voting-app
		ports:
			- 5000:80
		depends_on:
			- redis
	result:
		image: result-app
		ports:
			- 5001:80
	worker:
		image: worker
```

#### Docker compose verion 3

Docker compose verion 3 which is the latest, its simaler like 2, but has support docker swarm.

Filename: docker-compose.yml

```
version: 3
services:
	redis:
		image: redis
	db:
		image: postgres:9.4
	vote:
		image: voting-app
		ports:
			- 5000:80
	result:
		image: result-app
		ports:
			- 5001:80
	worker:
		image: worker

```


#### Docker compose verion 2 creating 2 diffrent networks

Filename: docker-compose.yml

```
version: 2
services:
	redis:
		image: redis
		networks:
			- back-end
	db:
		image: postgres:9.4
		networks:
			- back-end
	vote:
		image: voting-app
		ports:
			- 5000:80
		depends_on:
			- redis
		networks:
			- front-end
			- back-end
	result:
		image: result-app
		ports:
			- 5001:80
		networks:
			- front-end
			- back-end
	worker:
		image: worker
		networks:
			- back-end

networks:
	front-end:
	back-end:  
```  


### Private docker registry

`docker login private-registry.io`

`docker run private-registry.io/apps/internal-app`

#### Deploy Private Registy

`docker run -d -p 5000:5000 --name registry registry:2`

`docker image tag my-image localhost:5000/my-image`

`docker push localhost:5000/my-image`

`docker pull localhost:5000/my-image`


### Docker engine

docker engine can be run remotly

`docker -H=remote-docker-engine:2375`

`docker -H=10.123.2.1:2375 run nginx`


### Cgroups

Can use to limit resorce

`docker run --cpus=.5 ubuntu`

`docker run --memory=100m ubuntu`

### Cotainer orchestration - swarm

- Docker Swarm
- Kubernetes
- Mensos


[Add an UI using Portainer - video](https://www.youtube.com/watch?v=ZrEllmXDiwo)
