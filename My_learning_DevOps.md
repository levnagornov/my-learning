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

`YUM` is a high level package manager, it understands the package dependencies and install them automatically. Under the hood `YUM` uses `RPM`
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

Docker can restart the failed layer and if you change or add another layer the other layers will be ontouchend.  

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
