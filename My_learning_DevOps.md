# DevOps course

## Linux

### VI editor

Actions in Command mode:

- Move around with `arrow keys` or with `K J H L` (up, down, left, right)
- `x` to Delete a character
- `dd` to delete the line
- `yy` to copy a line
- `p` to paste a line
- `ctrl + u` scroll up
- `ctrl + d` scroll down
- `:w` to save changes
- `:w myfile.txt` to save changes into a new file myfile.txt
- `:q` to quit without save
- `:wq` to save and quit
- `/hello` to find a word hello, the cursor will be on the finding, press `n` to go to the next one

### Basic commands

```bash
whoami  # check the current user
id      # more detailer info about current user
su tom  # change current user to tom 

ssh tom@192.168.1.2 # to login into another 192.168.1.2 server as user tom

curl http://www.some-site.com/some-file.txt -O  # download a file from the web and save into a file

wget http://www.some-site.com/some-file.txt -O myfile.txt # another tool to download a file from the web and save into a file

ls /etc/*release* # to check the OS version
cat /etc/*release* # to check the OS version details
```

### Package managers

#### RPM (Red Hat Package Manager)

`CentOS` uses `RPM package manager` (Red Hat Package Manager).  
The software looks like a file with `.rmp` extension, for example `telnet.rpm`
Commands:

```bash
rpm -i telnet.rpm   # to install a package telnet
rpm -e telnet       # to uninstall the package
rpm -q telnet       # to get detailed info about the installed package
```

> **IMPORTANT NOTE:** RPM is a low-level package manger, it doesn't know about the dependencies and will not install them.

#### YUM

`YUM` is a high level package manager, it understands the package dependencies and install them automatically. Under the hood `YUM` uses `RPM`.

Commands:

```bash
yum install ansible  # will install ansible and python as a dependency 
```

`YUM` uses a special file `/etc/yum.repos.d`. You can add additional repositories to that file.

```bash
yum repolist # shows available repositories
ls /etc/yum.repos.d # shows the repositories as files
cat /etc/yum.repos.d/CentOS-Base.repo # shows web link of the repository, the link show the RPM files that YUM will download
```

#### Other package managers

Ubuntu, Linux Mint, Debian are using `APT`:

```bash
sudo apt update
sudo apt install vim
```

Fedora OS uses `DNF`:

```bash
sudo dnf install nano
```

### Main dirs

The main dirs in any Linux:

`/`     Root directory, contains other directories.

`/home` Home dirs of users, e.g. `/home/johndoe`.

`/etc`  System configuration files.

`/var`  Dynamic files, like logs or temporary files.

`/tmp`  Temp files, removed regularly by OS.

`/dev`  Connected devices, like USB, hard drive and etc.

`/mnt`  Mounts of external devices, like USB and other storage.

### Advanced commands

Creating, moving and removing, coping dirs and files

```bash
# Create a path
mkdir -p parent_folder/child_folder/sub_child

# Create a path with several dirs
mkdir -p project/{src,docs,tests}

# Creating nested structure
mkdir -p website/{assets/{css,js},content}

# Create several files
touch file1.txt file2.txt file3.txt

# Remove several files
rm file1.txt file2.txt

# Copy several files in a dir
cp file1.txt file2.txt file3.txt backup/

# Copy the whole structure
cp -r my_folder backup_folder
```

Reading files with `cat`

```bash
# Usage
echo "Hello, Linux!" > example.txt
cat example.txt
# Prints out
# "Hello, Linux!"

# Cat can be also used for combining files
cat file1.txt file2.txt > combined.txt

```

Reading file with `less`

```bash
# Usage
less example.txt
```

Important keys for `less`:
`Space`      Next page.

`b`          Previous page.

`/sometext`  Search from top to bottom.

`?sometext`  Search from bottom to top.

`q`          Exit less.

Reading file with `nano`

```bash
# Usage
nano example.txt
```

Important keys for `nano`:

`Ctrl+O`  Save the file.

`Ctrl+X`  Exit nano.

`Ctrl+K`  Cut line.

`Ctrl+U`  Paste line.

## Docker

### Commands

```shell
# Run a container
docker run <image-name-here>
docker run nginx  # will download the image if it's missing locally 

# Check status of containers
docker ps     # shows running containers statuses
docker ps -a  # shows all containers statuses

# Stop a container
docker stop <container-id>    # check docker ps to get the container id
docker stop <container-name>  # check docker ps to get the container name
docker stop silly-sammet

# Remove stopped container
docker rm <container-name>
docker stop silly-sammet

# Check list of images locally
docker images

# Remove image
docker rmi <image-name>
docker rmi nginx  # IMPORTANT check the dependent containers first!

# Download image 
docker pull <image-name>
docker pull nginx
```

By the default the `run` command will exit the container as soon it's finished it's process or if it's failed. So if you run `docker run ubuntu` it will exit immediately.  

We can execute a command for a container:

```bash
docker run ubuntu sleep 50

# Execute a command for a running container 
docker exec <container-name> cat /etc/hosts
```

By default when you start a container, you start it in `attached` mode:

```bash
docker run kodekloud/simple-webapp
# will print the container output here
```

You can start a container in background or in `detached` mode:

```bash
docker run -d kodekloud/simple-webapp  # IMPORTANT -d not at the end!
```

If you want to `attach` container again:

```bash
docker attach <container-id>
docker attach a043d
```

To run container and go inside:

```bash
docker attach -it <container-id> bash
docker attach -it centos bash
```

Stop all the containers:

```bash
docker ps | awk '{ print $1}' | tail +2 | xargs docker stop
```

Remove all the containers:

```bash
docker ps -a | awk '{ print $1}' | tail +2 | xargs docker rm
```

Remove all the images:

```bash
docker images | awk '{ print $1}' | tail +2 | xargs docker rmi
docker images | awk '{ print $3}' | tail +2 | xargs docker rmi
```

### Tags

To find all the supported tags for a image to to docker hub website

```bash
docker run redis      # redis:latest, so the tag=latest
docker run redis:4.0  # redis:4.0, so the tag=4.0
```

### Docker STDIN or why you need -it

If you have a simple application that will prompt you a question for your name and afterwards print welcome message with your name:

```bash
~/prompt-application$ ./app.sh
# Output + input
Please enter your name: John
Hello and welcome John!
```

By default docker containers **DO NOT** listen to standard input `STDIN` and it will not wait until you provide input, so it will print as "". It doesn't have a terminal to read inputs from and it runs in a non interactive mode:

```bash
docker run kodekloud/simple-prompt-docker
# Output
Hello and welcome !
```

If you want to provide an input, then you must to map the `STDIN` of your host to the docker container with `-i` - **interactive mode**:

```bash
docker run -i kodekloud/simple-prompt-docker
# Input
John
# Output
Hello and welcome John!
```

But the prompt is still missing, because the app's prompt is on the terminal and we have not attached to the containers terminal. For this we should use `-t` option:

```bash
docker run -it kodekloud/simple-prompt-docker
# Output + input
Please enter your name: John
Hello and welcome John!
```

### PORT mapping

For example if we run a simple web app, the app expects users on port `5000`:

```bash
docker run kodekloud/webapp
# Output
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

The default IP of a docker container is `172.17.0.2` or check with:

```bash
docker ps
docker inspect container_id | grep '"IPAddress"' | head -n 1
```

But `172.17.0.2` is an internal IP and users outside of the Docker Host, for this we need to use this IP `192.168.1.5` for Linux and `http://127.0.0.1/` for Win. But to make it work, you must have mapped the port inside the docker container to a free port on the docker host.

So I users want to access my port `80` (host) we can map local port `80` to docker container port `5000` like this with `-p` parameter:

```bash
docker run -p 80:5000 kodekloud/webapp
# Output
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
# PS
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS         PORTS                                     NAMES
3cf2aa2899ee   kodekloud/webapp   "/bin/sh -c 'FLASK_A…"   5 seconds ago   Up 4 seconds   0.0.0.0:80->5000/tcp, [::]:80->5000/tcp   elastic_buck
```

So <http://127.0.0.1:80/> will be accessible.

The port mapping allows you to run multiple instances of application but bind them to a different ports:

```bash
docker run -p 80:5000 kodekloud/webapp
docker run -p 8000:5000 kodekloud/webapp
docker run -p 8001:5000 kodekloud/webapp
docker run -p 3306:3306 mysql
docker run -p 8306:3306 mysql
```

### Volumes

How to persist data.

```bash
# You have a db
docker run mysql

# And let's say that data stored at this location inside the container
/var/lib/mysql

# So if you stop and remove the container all the data will be gone
docker stop mysql
docker rm mysql

# To secure the data, map the dir to outside
docker run -v /opt/datadir:/var/lib/mysql mysql
```

### Inspect a container

To get all the detail run inspect:

```bash
docker inspect container_name
```

### Container logs

For example you started a container in a detached mode `-d`:

```bash
docker logs container_name
docker logs container_id
```

### Jenkins example

<https://github.com/jenkinsci/docker/blob/master/README.md>

```bash
# admin - admin
docker run  -p 8080:8080 -p 50000:50000 -v my_jenkins_data:/var/jenkins_home jenkins/jenkins
```

### Images

How to create my own image if I build a flask app?

1. Choose OS - ubuntu
2. Update apt repo
3. Install dependencies using apt
4. Install Python dependencies using pip
5. Copy source code to /opt folder
6. Run the web server using flask command

Here is the docker file of the same:

```Dockerfile
FROM ubuntu
RUN apt-get update && apt-get -y install python python-pip
RUN pip install flask flask-mysql
COPY . /opt/source-code
ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```

And to run the container:

```bash
# Build an image from Dockerfile
docker build . -f Dockerfile -t levnagornov/my-flask-app

docker login

# Push docker image to Docker Hub - registry
docker push levnagornov/my-flask-app
```

`Dockerfile` is a file in instruction-argument format without extension. Every `Dockerfile` must start with `FROM`.  

Docker builds image in layers. Every step can take space, for example:

1. Base Ubuntu layer - 120 MB
2. Changes in apt packages - 306 MB
3. Changes in pip packages - 6.3 MB
4. Source code - 229 B
5. Update entrypoint with flask command - 0 B

You can find this info by `history` command:

```bash
docker history levnagornov/my-flask-app
```

Docker can restart the failed layer and if you change or add another layer the other layers will be untouched.  

Docker caches all the image layers.

### Env variables

Some application can use env variables inside the code:

```python
import os
from flask import Flask

app = Flask(__name__)

# color = "red"
color = os.environ.get('APP_COLOR')

@app.route("/")
def main():
    print(color)
    return render_template("hello.html", color=color)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port="8080")
```

To run the app:

```bash
export APP_COLOR=blue;
python app.py
```

You can pass this environment variable with docker variables:

```bash
docker run -e APP_COLOR=blue simple-webapp-color
docker run -e APP_COLOR=green simple-webapp-color
docker run -e APP_COLOR=pink simple-webapp-color
```

How to inspect the given variables to a container?

```bash
docker inspect container_name
# Output
[
    ...
    "Config":
        ...
        "Env": [
                "APP_COLOR=blue",
                ...
            ],
]
```

### Command vs Entrypoint

```bash
# Dockerfile
FROM ubuntu
CMD sleep 5

# There are 2 ways of using CMD:
# 1. CMD command param_1 or CMD sleep 5
# 2. CMD ["command", "param_1"] or CMD ["sleep", "5"] 

docker build . -t ubuntu-sleeper
docker run ubuntu-sleeper

# But if want to sleep 10 seconds, we need to run
docker run ubuntu-sleeper sleep 10 # which is weird and there is a duplication

# To change the hardcoded 5 to a variable we need to change the dockerfile
# New Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep"]

docker run ubuntu-sleeper 10

# IMPORTANT NOTE: if you forget to pass 10 in this case, it will fail, because sleep command expects a parameter!

# We can fix it by using ENTRYPOINT and CMD together:
# New Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep"]
CDM ["5"]  # default value if user didn't provide input

# IMPORTANT NOTE: User input always overwrites the CMD!

# If need we can overwrite the ENTRYPOINT:
docker run --entrypoint sleep2.0 ubuntu-sleeper 10 
```

### Docker compose

If need to run a few containers, it's better to use `docker compose`. It's an `YAML` file that contains logic about what and how to run.

> IMPORTANT NOTE: `Docker Compose` will run the containers on `one` local host!

Example:

```bash
# You need to run these 4 containers
docker run mmumshad/simple-webapp
docker run mongodb
docker run redis:alpine
docker run ansible
```

We can replace these 4 commands with one docker compose file:

```yaml
services:
    web:
        image: "mmumshad/simple-webapp"
    database:
        image: "mongodb"
    messaging:
        image: "redis:alpine"
    orchestration:
        image: "ansible"
```

To run docker-compose.yaml:

```bash
docker-compose up 
# or
docker compose up
```

### Voting application example

Voting-app (python) -> In-memory DB (redis) -> Worker (.NET) -> DB (postgres) -> Result-app (nodejs)

To start with `docker run`:

```bash
docker run -d --name=redis redis
docker run -d --name=db postgres
docker run -d --name=vote -p 5000:80 voting-app
docker run -d --name=result -p 5001:80 result-app
docker run -d --name=worker worker

# but those will fail, because we didn't linked them together
```

We need to add `links` for `docker run` approach:

```bash
docker run -d --name=redis redis
docker run -d --name=db postgres
docker run -d --name=vote -p --link redis:redis 5000:80 voting-app
docker run -d --name=result -p --link db:db 5001:80 result-app
docker run -d --name=worker --link db:db --link redis:redis worker

# but those will fail, because we didn't linked them together
```

> IMPORTANT NOTE: to `link` a container it must have a `name`. This `name` will be used for linking

The same result can be achieved with `docker compose`:

```yaml
services:
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        image: voting-app
        port:
            - 5000:80
        links:
            - redis
    result:
        image: result-app
        port:
            - 5001:80
        links:
            - db
    worker:
        image: worker
        links:
            - redis
            - db
```

If we want to instruct docker to `build` image instead of `pulling` it from a Docker Hub, we can replace `image:` with `build:`. Here is the example:

```yaml
services:
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        build: ./vote
        ports:
            - 5000:80
        links:
            - redis
    result:
        build: ./result
        ports:
            - 5001:80
        links:
            - db
    worker:
        build: ./worker
        links:
            - redis
            - db
```

### Docker compose versions

`Version: "1"` is the oldest, the current one is `Version: "3.9"`

Example of `Version: "1"`:

```yaml
# NOTE: in version 1 we don't have to specify version - so the var version is absent here.
# NOTE: in version 1 we use links.
redis:
    image: redis
db:
    image: postgres:9.4
vote:
    build: ./vote
    ports:
        - 5000:80
    links:
        - redis
result:
    build: ./result
    ports:
        - 5001:80
    links:
        - db
worker:
    build: ./worker
    links:
        - redis
        - db
```

Limitations of `Version: "1"`:

- If you want to use network type other than `bridge` you CAN'T specify it in `Version: "1"`.
- If you want to use order of deployment, there is no way to specify that. For example, you CAN'T start DB before WEBAPP.

Example of `Version: "2"`:

```yaml
version: "2"  # NOTE: you MUST specify version to use version 2 or higher
services:     # NOTE: you MUST use services and put your app below it
    # NOTE: by default from version 2 docker create one BRIDGE network and will attach all the container to that network
    #       so the containers will be able to communicate with each other using SERVICE NAME. 
    #       You don't need to use link anymore in version 2.
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        build: ./vote
        ports:
            - 5000:80
        depends_on: # NOTE: version 2 introduce DEPENDS_ON future to specify the order of the start up.
            - redis
    result:
        build: ./result
        ports:
            - 5001:80
    worker:
        build: ./worker
        depends_on: # NOTE: version 2 introduce DEPENDS_ON future to specify the order of the start up.
            - redis
            - db
```

Example of `Version: "3"`:

```yaml
# NOTE: version 3 has support of Docker Swarm and stacks
version: "3"
services:
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        build: ./vote
        ports:
            - 5000:80
        depends_on:
            - redis
    result:
        build: ./result
        ports:
            - 5001:80
    worker:
        build: ./worker
        depends_on:
            - redis
            - db
```

Example of how to add `front-end` and `back-end` networks to a docker compose file:

```yaml
version: "3"
services:
    redis:
        image: redis
        networks:  # connect container to networks you want
            - back-end
    db:
        image: postgres:9.4
        networks:  # connect container to networks you want
            - back-end
    vote:
        build: ./vote
        ports:
            - 5000:80
        depends_on:
            - redis
        networks:  # connect container to networks you want
            - front-end
            - back-end
    result:
        build: ./result
        ports:
            - 5001:80
        networks:  # connect container to networks you want
            - front-end
            - back-end
    worker:
        build: ./worker
        depends_on:
            - redis
            - db

networks:  # declare new networks
    front-end:
    back-end:
```

### Docker registry

So when we run `docker run nginx`, here we have `image: nginx`. The full Docker's naming convention is `user/image` or in other words `account/repository` and for `nginx` it will be `library/nginx`. `Library` prefix is used when no specific account or repository is provided, indication an official Docker Hub image.

The `username` is usually your `account name` or `organization name`. For myself it will be `levnagornov/my-app`.

In fact there is also a `registry` specified at the background:
`docker.io/library/nginx`

The DNS name for registry is `docker.io`.

When push or pull an image it comes from the registry - in this case `docker.io`.

There are other popular `public registries`:

- Google registry `gcr.io` (mainly kubernetes)
- Amazon
- ...

But there is also an option to use `private registries`. So these repositories will be accessible only with using a set of credentials.

How to use a `private registry`:

- Login with `docker login my-private-registry.io` command and provide username and password.
- Then you use the image like this `docker run my-private-registry.io/apps/some-internal-app`

> NOTE: Remember to always `login` before pulling or pushing a private image!

To use private localhost registry - run it with the name of the image is registry and expose the API on port 5000:

```bash
docker run -d -p 5000:5000 --name registry registry:2
```

Now you can push an image to this private registry:

- Tag the image first `docker image tag my-image localhost:5000/my-image`
- Push with `docker push localhost:5000/my-image`
- Then you can pull this image within the network like this `docker pull localhost:5000/my-image` or `docker pull 192.168.56.100:5000/my-image`, the second command is for accessing from another host but in the same environment.

### Docker engine

`Docker engine` is referred to a host with Docker installed on it. It contains:

- `Docker CLI`
- `Docker REST API server`
- `Docker Daemon`

`Docker Daemon` is a background process that manages Docker objects, such as images, containers, volumes and networks.

`Docker REST API server` is the API interface that programs can use to talk to the daemon and provide instructions. You can create your own tools with it.

`Docker CLI` is command line interface for interaction via a terminal. It uses `Docker REST API server` to give commands to `Docker Daemon`.

> Note: `Docker CLI` need NOT necessarily to be installed on the same host. It could be on another system like a laptop and can still work with a remote `Docker engine`. To use it specify `-H` option like this `docker -H=remote-docker-engine:2375`. Real example to run NGINX `docker -H=10.123.2.1:2375 run nginx`

#### Containerization

Under the hood Docker uses **namespaces** to isolate workspace:

- Process IDs
- Networks
- Mounts
- InterProcesses
- Unix timesharing

These all are created in their own namespace and this provide the isolation between the containers.

For example of the techniques is process ID namespace. On your host you can have already processes with ID #1, #2, #3 and #4 and you boot up a container where the underlying Linux system also wants to start processes with ID #1 and #2. And since there is no hard isolation between the container and the running host, so the processes running in the container are in fact running on the host. And two processes can't have the same ID, here the namespace is used. Each process can have multiple process IDs associated with it. So on host processes #5 and #6 will be created and those will me mapped as processes #1 and #2 in container.

```text
        Linux system
PID: 1
- PID: 2                             |
- PID: 3    Child system (container) v
- PID: 4     ________________________
- PID: 5 -> | PID: 1                 |
- PID: 6 -> | - PID: 2               |
            |________________________|
```

#### cgroups

`Docker host` and `Docker containers` are **sharing** the same systems resources: `CPU` and `Memory`.

> NOTE: By the default there is NO restriction how much of a resource a container can use.

We should restrict the usage, otherwise a container can use all the resources of the host. `Docker` uses `cgroups` or `control groups` to restrict the amount of hardware resources allocated to each container. You can use `--cpu` and `--memory` to specify the restriction:

```bash
# Limit to 50% usage of the host CPU
docker run --cpu=.5 ubuntu

# Limit to 100 megabytes usage of the host RAM
docker run --memory=100m ubuntu
```

More about resource constraints here - [Docker resource constraints](https://docs.docker.com/engine/containers/resource_constraints/)

### Docker storage

#### Docker file system

When Docker is installed, the default location is `/var/lib/docker`, it creates the following dirs:

```text
/var/lib/docker/
- aufs/
- containers/
- image/
- volumes/
...
```

#### Docker layered architecture

When Docker builds images, it builds these in a layered architecture. Each line of instruction in the `Dockerfile` creates a new layer in the Docker image with just the changes from the previous layer. For example:

```Dockerfile
# Dockerfile
FROM ubuntu
RUN apt-get update && apt-get -y install python
RUN pip install flask flask-mysql
COPY . /opt/source-code
ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run

# Layer 1. Base ubuntu layer                                       | size 120 MB
# Layer 2. Changes in apt packages (update and install python)     | size 306 MB
# Layer 3. Changes in pip packages (install flask and flask-mysql) | size 6.3 MB
# Layer 4. Copy source code                                        | size 229 B
# Layer 5. Update the entrypoint                                   | size 0 B

# docker build Dockerfile -t user/my-custom-app
```

> NOTE: Each layer only stores the changes from the previous layer and it's reflected in the size.

Now if we have another `Dockerfile2` and we want to build and image. Here we have the same base image, the same python and flask. So Docker will use the layers from the previous app:

```Dockerfile
# Dockerfile2
FROM ubuntu
RUN apt-get update && apt-get -y install python
RUN pip install flask flask-mysql
COPY app2.py /opt/source-code
ENTRYPOINT FLASK_APP=/opt/source-code/app2.py flask run

# Layer 1. Base ubuntu layer                                       | size 0, will be reused
# Layer 2. Changes in apt packages (update and install python)     | size 0, will be reused
# Layer 3. Changes in pip packages (install flask and flask-mysql) | size 0, will be reused
# > Layer 4. Copy source code                                      | will be created
# > Layer 5. Update the entrypoint                                 | will be created

# docker build Dockerfile -t user/my-custom-app2
```

This allows Docker to build images faster and efficiently saves space. This is also applicable when you just update the application code!

> NOTE: Once the `build` is complete, you can NOT modify the the contents of these layers, they are `read only` now. When you execute the `docker run` it actually creates `Layer 6.` - `Container layer` and this layer is `writable`. This writable layer is used for storing data and files that is created by running application inside. This file be removed once the container exits, unless `volume` or `mount` bind is setup.

#### Volumes and bind mounting

If we have a DB and we want to save the data during the run of a container, we should use `volumes`. We can create a volume by running this command:

```bash
docker volume create my_data_volume
```

It will create a dir:

```text
/var/lib/docker/
- volumes/
- - my_data_volume/
```

To mount this created volume use `-v` (old way) or `--mount` (new way):

```bash
# Syntax
docker run -v <volume_name_on_host>:<path_in_container> image_name

# MySQL example:
docker run -v my_data_volume:/var/lib/mysql mysql
```

After container is destroyed, the data will remain in the `/var/lib/docker/volumes` and when we start the container again, the data can be used.

> NOTE: If you run `docker run` with `-v` and new volume name, docker will create a new volume automatically.

This is called `volume mounting` as we are mounting in `/var/lib/docker/volumes`.  
There is also a second type of mounting. If we would like to use a different location with existing data, we can use `bind mounting`:

```bash
# Syntax
docker run -v <path_for_mount_on_host>:<path_in_container> image_name

# MySQL example:
# NOTE: we are using a custom, not default location!
docker run -v /my_data/mysql:/var/lib/mysql mysql
```

This is **new and recommended way** of mounting with `--mount` instead of `-v`:

```bash
# Old way
docker run -v /my_data/mysql:/var/lib/mysql mysql

# New way
docker run \
    --mount type=bind,source=/data/mysql,target=/var/lib/mysql \
    mysql
```

#### Storage drivers

`Storage drivers` are responsible for:

- maintaining the layered architecture
- creating a writable layer
- moving file across the layers
- etc...

So `Docker` uses `storage drivers` to enable layered architecture.

These are the common storage drivers:

- AUFS
- ZFS
- BTRFS
- Device Mapper
- Overlay
- Overlay2

The selection of the storage driver depends on the underlying OS being used.

`Debian` and `Ubuntu` are using `AUFS`, but it's not available for `Fedora` or `CentOS`. Use `Device Mapper` there instead. Docker will choose the storage driver automatically.

Different storage drivers provide different performance and speed characteristics.

To find the currently used `storage driver` run:

```bash
docker info | grep -i storage

# Output
# Storage Driver: aufs
#  Root Dir: /var/lib/docker/aufs
```

The structure of `/var/lib/docker/aufs`:

```text
/var/lib/docker/aufs/
- diff/
- layers/
- mnt/     # mount
```

Each `storage driver` stores data differently.

To check disk size is used by docker run:

```bash
docker system df
docker system df -v # break down by image
```

### Docker networks

When docker is installed, 3 networks will be created automatically:

- bridge
- none
- host

By default a container will be attached to `bridge` network. To attach it to another network use `--network <network_name>`:

```bash
docker run ubuntu --network=none
docker run ubuntu --network=host
docker run ubuntu --network my_network
```

`Bridge` network is a private internal network and the containers will get an internal IP address, usually in range `172.17`, for example `172.17.0.2`, `172.17.0.5`. To access this from outside world we have to `map` the `container ports` with the `host ports`.

To create another network use `docker network create` command:

```bash
docker network create \
  --driver bridge \
  --subnet 182.18.0.0/16 \
  my-custom-isolated-network
```

To check network settings of a container use `docker inspect` command:

```bash
docker inspect my_container

# Output
# ...
# "Networks": {
#     "bridge": {
#         "Gateway": "172.17.0.1"
#         "IPAddress": "172.17.0.6",
#         "MacAddress": "02:42:ac:11:00:06"
#     }
# }
# ...
```

Example of connecting a network to a container:

```bash
docker run --name webapp \
  -d \
  --network my-super-network \
  -e DB_Host=mysql-db \
  -e DB_Password=db_pass123 \
  -p 38080:8080 \
  -l mysql-db:mysql-db \
  user/my-simple-webapp-mysql
```

Docker has `embedded DNS`, so container can reach out each other by a name:

```bash
# network bridge:
# web container on 172.17.0.2
# mysql container on 172.17.0.3

# Code inside web container
mysql.connect(172.17.0.3)   # not recommended as the IP can change
# or
mysql.connect(mysql)        # correct way, use Docker DNS
```

Docker uses namespaces for networks. It creates a separate namespace for each container and then it uses virtual Ethernet pairs to connect containers together.

### Container orchestration

By default you manage one host and containers on it, by manually running `docker run nodejs`. And if user load increases you will need to manually create more containers and also watch their health and create new if some of them crashes. To solve this `container orchestration` solutions are used. Those are set of tools and scripts.

Typically a `container orchestration` contains several `hosts`. It's required because if one host fails, the others will remain accessible.

```bash
docker service create --replicas=10 nodejs
```

Some orchestration solutions help you to increase or decrease the number of instances according to users load. There are other features available:

- load balancing
- security
- advanced networking
- etc...

Available solutions:

- `Docker Swarm` from Docker
- `Kubernetes` from Google
- `MESOS` from Apache

`Docker Swarm` is easy to set up and use, but it lacks some advanced features like auto scaling.

`MESOS` is quite difficult to setup, but it has many advanced features.

`Kubernetes` is the most popular and it has also many advanced features. It's supported on all public cloud service providers like Google Cloud, Azure, AWS.

#### Docker Swarm

In `Docker Swarm` you can combine multiple Docker machines into a single cluster. So `Docker Swarm` will take care of distributing application instances into a separate host for high availability and load balancing. To use `Docker Swarm` you must have several hosts with Docker on them. Then you need to declare one host as `Master`/`Swarm Manager` and `Slaves`/`Workers`.

```bash
# Run on master host / swarm manager host
docker swarm init
# The output will provide a command to be run on workers:
# something like
docker swarm join --token <token>
```

After joining the swarm `workers` will be referred as `nodes`. Now you can create services and deploy them on a swarm cluster.

To run several instances of the app/container across worker nodes in the swarm cluster we should use `services`:

```bash
# Must be run on master host / swarm manager host
docker service create --replicas=3 my-web-app
```

The `docker service` command is similar to `docker run` command in terms of options to pass:

```bash
# Must be run on master host / swarm manager host
docker service create --replicas=3 \
    --network frontend \
    -p 8080:80 \
    my-web-app
```

#### Kubernetes

`Kubernetes` cluster has also `nodes`.

Kubernetes has `kubernetes CLI` aka `kube control`. You can run a thousand instances of the same application with a single command:

```bash
kubectl run --replicas=1000 my-web-app
```

To automatically scale your app:

```bash
kubectl run --scale=2000 my-web-app
```

Kubernetes can be configured that instances and the infrastructure itself can scale up and down based on user load.

Kubernetes can upgrade these 2000 apps in a rolling update fashion one at a time with a single command:

```bash
# To apply rolling-update:
kubectl rolling-update my-web-app --image=my-web-app-2

# To revert it:
kubectl rolling-update my-web-app --rollback
```

Kubernetes can help you test new features of your application by only upgrading a percentage of these instances through A-B testing methods.

Kubernetes uses Docker hosts to host applications in the form of Docker containers. But it can also use other containers.

Kubernetes architecture:

- `Node` can be a physical or virtual machine where Kubernetes software is installed. `Node` is a `worker` machine as well, so it's where containers are running.
- `Cluster` is a set of `nodes`, because having one node is not enough, it can crash, so you will lose the availability.
- `Master node` is a node with the Kubernetes control plane components installed. `Master node` is watching the other `nodes`, and he is responsible for the actual orchestration of the containers.

When you install Kubernetes, you will install the following components:

- API server
- etcd server
- kubelet service
- Container runtime (Docker for example)
- Controller
- Scheduler

`API server` is like a frontend for Kubernetes user's management devices, CLI, they all talk to `API server` to interact with the component cluster.

`etcd` is distributed reliable key-value store used by Kubernetes. It stores all data to manage the cluster.

`Scheduler` is responsible for distributing work or containers across multiple nodes.

`Controller` is responsible for noticing and responding when nodes or containers closed down, the controllers decide to bring up new containers.

`Container runtime` is the software than runs containers, for example Docker.

`Kubelet` is the agent that runs on each node in the cluster. It's responsible for making sure that containers are running as expected.

`kubectl` or `kubernetes CLI` or `kube control tool` or `kube cuddle` is used to deploy and manage applications on a Kubernetes cluster.

Commands:

```bash
# To deploy an application on a cluster:
kubectl run hello-world-app 

# To view info about the cluster
kubectl cluster-info

# To list all the nodes that are part of the cluster
kubectl get nodes
```
