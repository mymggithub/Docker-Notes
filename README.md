# Docker-Notes
Notes on docker

Installing docker has an easy way using the convient script with linux

#### Check the version

`sudo docker version`

### Containers

#### -List running containers

`docker ps`

#### -List all containers

`docker ps -a`

#### -Starts container first time

`docker run docker-username/file-name run-name`

#### -Stop a container

`docker stop [provide name or ID]`

#### -Remove a container

`docker rm  [provide name or ID]`

#### Remove all containers

`docker rm $(docker ps -q)`

#### Remove exited containers

`docker rm $(docker ps -a -f status=exited -q)`

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

`docker run -d docker-username/file-name run-name`

#### -Run Attach mode

`docker attach [provide name or ID]`

#### -Run an latest version

`docker run docker-username/file-name:latest`

#### -Run an older version :0.0

`docker run docker-username/file-name:4.0 run-name`

#### -Run STDIN -i interactive mode -t terminal

`docker run -it docker-username/file-name run-name`

#### -Run port mapping out-container:in-container

`docker run -p 80:5000 docker-username/file-name run-name`

#### -Run volume mapping -v /folder/folder/folder out:in

`docker run -v /opt/datadir:/var/lib/mysql docker-username/file-name run-name`

#### -Container details

`docker inspect run-name`

#### -Container logs
`docker logs run-name`

#### -Environment variables

`docker run -e APP_COLOR=blue docker-username/file-name run-name`

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
- COPY - Copy source code
- ENTRYPOINT - Specify entrypoint, a command that will run when an image container

#### -Build the docker

`docker build Dockerfile -t docker-username/my-custom-app`

#### -Push to public docker hub

`docker push docker-username/my-custom-app`

#### -You can see the history of the build using

`docker history docker-username/my-custom-app`



