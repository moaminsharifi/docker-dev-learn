# docker-fast-learn
learn most important part of docker fast and easy
### check docker version


```python
!docker --version
```

    Docker version 19.03.8, build afacb8b7f0


### run first docker container


```python
!docker run hello-world
```

    
    Hello from Docker!
    This message shows that your installation appears to be working correctly.
    
    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.
    
    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash
    
    Share images, automate workflows, and more with a free Docker ID:
     https://hub.docker.com/
    
    For more examples and ideas, visit:
     https://docs.docker.com/get-started/
    


# what happend when you run it?

1. check image file with image and and tag, \
    in here we need hello-world:latest \
    *hello-world* is image name \
    *latest* default image tag to get latest image
    
2. if not exist get from DockerHub

3. run it with your parameters

![what happend when you run it](https://docs.docker.com/engine/images/architecture.svg)

# run  nodeJS project with docker

1. get projet from git
2. check docker file
3. run it




```python

# go to some path like workspace
%cd ~/code/python/docker
# clone it
!git clone https://github.com/dockersamples/node-bulletin-board
# change dir in to project foulder
%cd node-bulletin-board
# list of files
%ls
%cd bulletin-board-app
```

    /home/amin/code/python/docker
    fatal: destination path 'node-bulletin-board' already exists and is not an empty directory.
    /home/amin/code/python/docker/node-bulletin-board
    [0m[01;34mbulletin-board-app[0m/  LICENSE
    /home/amin/code/python/docker/node-bulletin-board/bulletin-board-app



```python
# list of our files
%ls
# show Dockerfile sample
%cat Dockerfile
```

    app.js    Dockerfile  index.html  package.json  server.js
    [0m[01;34mbackend[0m/  [01;34mfonts[0m/      LICENSE     readme.md     site.css
    FROM node:current-slim
    
    WORKDIR /usr/src/app
    COPY package.json .
    RUN npm install
    
    EXPOSE 8080
    CMD [ "npm", "start" ]
    
    COPY . .

### check line by line

1. get some base image
```python
FROM node:current-slim
```
[more information about this image](https://hub.docker.com/_/node/) \
this image provide a base for nodejs project 

2. set work directory
```python
WORKDIR /usr/src/app
```
3. copy packge file to workdir 
```python
COPY package.json .
```
4. install packges
```python
RUN npm install
```

5. set port 
```python
EXPOSE 8080
```
when you EXPOSE some port tell to container "*hey yo, set this port open from out of container*"
and EXPOSE some like -p or --public flag.

6. set which commad alway be run
```python
CMD [ "npm", "start" ]
```

7. then copy file files into container
```python
COPY . .
```

## now let's build it
build mean put all project and base into Single image


```python
# --tag name:version some thing like that
!docker build --tag bulletinboard:1.0 .

```

    Sending build context to Docker daemon  45.57kB
    Step 1/7 : FROM node:current-slim
     ---> 8d32307afb40
    Step 2/7 : WORKDIR /usr/src/app
     ---> Using cache
     ---> 25bd7f80dcf3
    Step 3/7 : COPY package.json .
     ---> Using cache
     ---> 8153ef420259
    Step 4/7 : RUN npm install
     ---> Using cache
     ---> 5eeb0f4e732c
    Step 5/7 : EXPOSE 8080
     ---> Using cache
     ---> d2169c55a8e7
    Step 6/7 : CMD [ "npm", "start" ]
     ---> Using cache
     ---> 4364353391aa
    Step 7/7 : COPY . .
     ---> Using cache
     ---> 523e1a8df7fd
    Successfully built 523e1a8df7fd
    Successfully tagged bulletinboard:1.0


## Let's run  image


```python
!docker run --publish 8000:8080 --detach --name b1 bulletinboard:1.0
# now let's check 127.0.0.1:8000
```

    docker: Error response from daemon: Conflict. The container name "/b1" is already in use by container "74d28aaf90293675e600f1c6baa2123fac34afbd0c719fde28537e1a81c3ec56". You have to remove (or rename) that container to be able to reuse that name.
    See 'docker run --help'.


### stop and then delete


```python
!docker stop b1

!docker rm b1
```

    b1
    b1


### then what happend there?
#### Let's check it.

- it'is our base command
```python
docker run
```

-  when all command run and container up then let container to do it's work or in other word when container run
    disconnect from it
   
```python
--detach
```   
- set any request in 8000 port of host map to 8080 port of container
    
```python
--publish 8000:8080
``` 
- name of container and which image use
```python
--name b1 bulletinboard:1.0
``` 

    

## Volumes

### when we need volumes?
#### when need to save data out of container like database, logs, uploaded use file


#### for use there two type volumes almost we use
1. save data into docker volumens when use mysql or other database
2. use project code in  host system instead put it in image


#### there two way docker tell us to use volumes
1. --mount or -m
2. --volume or -v

and tell us to use *--mount* because easier syntax and I perfer to use it in docker build

### let's start and create new volume


```python
!docker volume create myvol

```

    myvol


### Now let's check list of volumes


```python
!docker volume ls
```

    DRIVER              VOLUME NAME
    local               1d3e0e5517dbae711167e7da3d125268dabe0027af862dd9dd0624e92614e111
    local               8c2e5f76aff591489bbcd11fcdd4cfda60ef4b4d20356ed7d9317e2945f04ac4
    local               49d37fa68a9367de719a67275f4952a36f21badb6da3952b99e89adfd618e567
    local               192f6942c1df22757d9aeda718a438ff94dec82a5ff62dfefdfb9e31f32b6267
    local               10638fa59b053b04e25092e4b744428e34822ec51b1c6a2ecabc4cfad2094bc4
    local               68935b8a8cedc7a4d345fc4936561ab9fc66698d84ab048aac9b2d4c8541b9b5
    local               af36ae140864ecb086ca69ce1c1710dd018a9daa4a5be71cf2f2847f527e3b50
    local               b3de1b1634b5093efa61ddc6557f7e6ed7974b784069366e4e4ba8e9a443f1a2
    local               b65ec3222350164251ea84b86c130c123eb38e05a439b98e40b3d2c8834b0c8d
    local               e191772680759d1396cad1f9b6a4b4e97d8b2e8171166f4a65a08f50adea33c1
    local               ff34a106ed413d75c11ad8e2a2fc9c2159392376d9a34c503345478f54a1d589
    local               myvol


### detail of volumes


```python
!docker volume inspect myvol

```

    [
        {
            "CreatedAt": "2020-05-04T15:42:20+04:30",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "/var/lib/docker/volumes/myvol/_data",
            "Name": "myvol",
            "Options": {},
            "Scope": "local"
        }
    ]


### Do You want remove some volume? let's do it


```python
!docker volume rm myvol
```

    Error response from daemon: remove myvol: volume is in use - [23c1cf9429299fa02a62a626520ca80d1668b076abe3b49cd1f7a5b1832b2560]


### Until Now You can understand volumes now how use it in container


```python
#here is sample with ngnix
!docker run --detach  --name devtest_ng  -v myvol:/app  nginx:latest
# here is sample with mysql
!docker run --detach  --name devtest_mysql  -v myvol:/var/lib/mysql  mysql

```

    6b24c77db0493c9386083bbc17925ff512bc73eefbe05d6fedee9d6d448a0dbc
    710858d522babd8a8875b15a14d83f71cfc53b335f4d9908ab5f26eb6e413f09


for using docker volume You most know where data of container to save in docker volume

#### now let's stop and delete those container


```python
!docker stop devtest_ng
!docker stop devtest_mysql

!docker rm devtest_ng
!docker rm devtest_mysql
```

    devtest_ng
    devtest_mysql
    devtest_ng
    devtest_mysql


## what about run from host in container?
### what we need? the best mini framwork in python. FLASK!
1. set directory

2. create base python and flask need file

3. set up Dockerfile

if you need something more powerful look at[digitalocean resource](https://www.digitalocean.com/community/tutorials/how-to-build-and-deploy-a-flask-application-using-docker-on-ubuntu-18-04)

### 1- set directory



```python
## create new folder for dear flask
## need to change 
%cd ~/code/python/docker
%ls
%mkdir flask
%cd flask
```

    /home/amin/code/python/docker
    flask_runer.sh  main.py  [0m[01;34mnode-bulletin-board[0m/  requirements.txt
    /home/amin/code/python/docker/flask


### 2- create base python and flask need file



```python
# create main.py
!echo "from flask import Flask \napp = Flask(__name__) \n@app.route('/') \ndef home():\n    return \"hello world!\"\nif __name__ == '__main__':\n    app.run(host='0.0.0.0') " >> main.py
!cat main.py
```

    from flask import Flask 
    app = Flask(__name__) 
    @app.route('/') 
    def home():
        return "hello world!"
    if __name__ == '__main__':
        app.run(host='0.0.0.0') 



```python
!echo "Flask" >> requirements.txt
!cat requirements.txt
```

    Flask


### 3- set up Dockerfile



```python
!echo "FROM python:alpine3.11 \nRUN apk --update add bash nano \nCOPY ./requirements.txt /var/www/requirements.txt \nRUN pip install --upgrade pip \nRUN pip install -r /var/www/requirements.txt \nWORKDIR /app \nEXPOSE 5000 \nCMD [\"python\", \"main.py\"]" >> Dockerfile
!cat Dockerfile
```

    FROM python:alpine3.11 
    RUN apk --update add bash nano 
    COPY ./requirements.txt /var/www/requirements.txt 
    RUN pip install --upgrade pip 
    RUN pip install -r /var/www/requirements.txt 
    WORKDIR /app 
    EXPOSE 5000 
    CMD ["python", "main.py"]


### check line by line

1. get some base image
```python
FROM python:alpine3.11  
```
[more information about this image](https://hub.docker.com/_/python)

2. update alpine and add nano editor to image
```python
RUN apk --update add bash nano 
```


3. move from requirements.txt from here to image
```python
COPY ./requirements.txt /var/www/requirements.txt 
```


4. update pip
```python
RUN pip install --upgrade pip
```
5. install all needed packge in requirements.txt
```python
RUN pip install -r /var/www/requirements.txt
```

6.  set where is out workspace
```python
WORKDIR /app
```

7. set which port for map from host
```python
EXPOSE 5000 
```

7. run ouy simple flask file
```python
CMD ["python", "main.py"]
```

_as You see in here use some CMD ["python","flask"] stuff because without any other tools to handle it. (it's fine for learning and development)_

## now let's build it
build mean put all project and base into Single image


```python
!docker build --tag our_flask_sample .
```

    Sending build context to Docker daemon   5.12kB
    Step 1/8 : FROM python:alpine3.11
    alpine3.11: Pulling from library/python
    
    [1Be7a5bc2a: Pulling fs layer 
    [1Bcd19a4e3: Pulling fs layer 
    [1Bdcbeccf1: Pulling fs layer 
    [1B327f91dd: Pulling fs layer 
    [1B0a120de2: Pull complete 931MB/1.931MBB[4A[2K[4A[2K[5A[2K[4A[2K[5A[2K[4A[2K[5A[2K[4A[2K[3A[2K[4A[2K[5A[2K[5A[2K[5A[2K[3A[2K[5A[2K[3A[2K[5A[2K[3A[2K[3A[2K[5A[2K[5A[2K[5A[2K[5A[2K[4A[2K[4A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[2A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[1A[2K[3A[2K[3A[2K[1A[2K[3A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[1A[2K[3A[2K[1A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[3A[2K[1A[2K[1A[2K[3A[2K[1A[2K[1A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[3A[2K[2A[2K[1A[2K[1A[2K[1A[2KDigest: sha256:745fac134e7ea2947934f4baba06db821e63db3607938692017f766c4834cbc0
    Status: Downloaded newer image for python:alpine3.11
     ---> 6c32e2504283
    Step 2/8 : RUN apk --update add bash nano
     ---> Running in acb4430d1e1c
    fetch http://dl-cdn.alpinelinux.org/alpine/v3.11/main/x86_64/APKINDEX.tar.gz
    fetch http://dl-cdn.alpinelinux.org/alpine/v3.11/community/x86_64/APKINDEX.tar.gz
    (1/3) Installing bash (5.0.11-r1)
    Executing bash-5.0.11-r1.post-install
    (2/3) Installing libmagic (5.37-r1)
    (3/3) Installing nano (4.6-r0)
    Executing busybox-1.31.1-r9.trigger
    OK: 18 MiB in 37 packages
    Removing intermediate container acb4430d1e1c
     ---> 5bd938a9cb0c
    Step 3/8 : COPY ./requirements.txt /var/www/requirements.txt
     ---> 052998ec5f15
    Step 4/8 : RUN pip install --upgrade pip
     ---> Running in 7d6ab76165fe
    Requirement already up-to-date: pip in /usr/local/lib/python3.8/site-packages (20.1)
    Removing intermediate container 7d6ab76165fe
     ---> 99d7b3fe14b0
    Step 5/8 : RUN pip install -r /var/www/requirements.txt
     ---> Running in 16dd1de31caa
    Collecting Flask
      Downloading Flask-1.1.2-py2.py3-none-any.whl (94 kB)
    Collecting click>=5.1
      Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
    Collecting Werkzeug>=0.15
      Downloading Werkzeug-1.0.1-py2.py3-none-any.whl (298 kB)
    Collecting itsdangerous>=0.24
      Downloading itsdangerous-1.1.0-py2.py3-none-any.whl (16 kB)
    Collecting Jinja2>=2.10.1
      Downloading Jinja2-2.11.2-py2.py3-none-any.whl (125 kB)
    Collecting MarkupSafe>=0.23
      Downloading MarkupSafe-1.1.1.tar.gz (19 kB)
    Building wheels for collected packages: MarkupSafe
      Building wheel for MarkupSafe (setup.py): started
      Building wheel for MarkupSafe (setup.py): finished with status 'done'
      Created wheel for MarkupSafe: filename=MarkupSafe-1.1.1-py3-none-any.whl size=12629 sha256=b00be5c2e40334aff8d20828f765d9989aeaf6461be072918affbd67ddaab5d3
      Stored in directory: /root/.cache/pip/wheels/0c/61/d6/4db4f4c28254856e82305fdb1f752ed7f8482e54c384d8cb0e
    Successfully built MarkupSafe
    Installing collected packages: click, Werkzeug, itsdangerous, MarkupSafe, Jinja2, Flask
    Successfully installed Flask-1.1.2 Jinja2-2.11.2 MarkupSafe-1.1.1 Werkzeug-1.0.1 click-7.1.2 itsdangerous-1.1.0
    Removing intermediate container 16dd1de31caa
     ---> 4ce7c32142de
    Step 6/8 : WORKDIR /app
     ---> Running in 6d7f52c6e365
    Removing intermediate container 6d7f52c6e365
     ---> 959af420e8ca
    Step 7/8 : EXPOSE 5000
     ---> Running in 95740b604ab1
    Removing intermediate container 95740b604ab1
     ---> 95a2b10fd0b3
    Step 8/8 : CMD ["python", "main.py"]
     ---> Running in de36c2117667
    Removing intermediate container de36c2117667
     ---> ef5938719b6a
    Successfully built ef5938719b6a
    Successfully tagged our_flask_sample:latest



```python
!docker run -d  --name our_flask_sample  --publish 12233:5000 -v ~/code/python/docker/flask:/app our_flask_sample
!docker ps
```

    c94502b0940c6732c5c9da82659050cf4b338362b08c276d6f7ffe1a6e2f22ff
    CONTAINER ID        IMAGE               COMMAND             CREATED                  STATUS                  PORTS                     NAMES
    c94502b0940c        our_flask_sample    "python main.py"    Less than a second ago   Up Less than a second   0.0.0.0:12233->5000/tcp   our_flask_sample


### Hi darling!
![hello world!](docker-run-flask.png)


```python
!docker stop our_flask_sample

!docker rm our_flask_sample
```

    our_flask_sample
    our_flask_sample


## here is some tricks
1. -v or volume need to pass two thing 
    - src
    - target
    
    like that -v ~/code/python/docker/flask:/app
    #### but that's not good way. You can use $PWD in your commend instead full path like that


```python
!docker run -d  --name our_flask_sample  --publish 12233:5000 -v $PWD:/app our_flask_sample
!docker ps
```

    e66c47607e358094fa429a1546359627268482350f9ff5780350bf223b08a352
    CONTAINER ID        IMAGE               COMMAND             CREATED                  STATUS                  PORTS                     NAMES
    e66c47607e35        our_flask_sample    "python main.py"    Less than a second ago   Up Less than a second   0.0.0.0:12233->5000/tcp   our_flask_sample

