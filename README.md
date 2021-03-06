# Redis

### What is Redis?
- An open-source, networked, in-memory data store.
- Written in Ansi C and is extremely fast. 
- Most popular Key-Value noSql Dataabse. 

### About the Image
This image definition Dockerfile uses multi stage builds and compiles redis from the source code.
Uses build-args to  the docker build process to  customize the redis version to be used and the OS base version to be used. 
If no build-args are provided, default tags used are redis:5.0.5 and ubuntu:latest, alpine:latest, centos:latest for the base OS image.

examples:- 
````
--build-arg ubuntu_codename=ubuntu:[disco:xenial:latest]  --build-arg redis_ver=[5.0|5.0.5|5.0.6|6.0.9]
--build-arg centos:[7|latest]  --build-arg redis_ver=[5.0|5.0.5|5.0.6|6.0.9]
--build-arg alpine:[latest:3.10.3] --build-arg redis_ver=[5.0|5.0.5|5.0.6|6.0.9]
````

| ubuntu version | ubuntu_codename  |
| -------------- | ---------------- |
|   20.04        | focal            |
|   20.10        | groovy           |
|   21.04        | hirsute          |
|   19.10        | eoan             |
|   19.04        | disco            |
|   18.04        | bionic           |
|   16.04        | xenial           |

#### Redis files and directories
|     path/directory           |    Description                     |
| ---------------------------- | ---------------------------------- |
| /opt/redis                   |  REDIS_HOME                        |
| /opt/redis/bin               |  Redis executable binaries         |
| /opt/redis/conf              |  Redis config files (redis.conf)   |
| /opt/redis/logs              |  Redis logs (image build logs)     |
| /opt/redis/data              |  Redis data store area             |

## Building the Redis Image
#### 1. redis on ubuntu
````
$sudo docker build --tag redis-ubuntu:6.0.9 --build-arg ubuntu_codename=latest --build-arg redis_ver=6.0.9 -f build/ubuntu/
$sudo docker push redis-ubuntu:6.0.9  
````
#### 2. redis on centos
````
$sudo docker build --tag redis-centos:6.0.9 --build-arg centos_ver:7 --build-arg redis_ver=6.0.9 -f build/centos/
$sudo docker push redis-centos:6.0.9
````
#### 3. redis on alpine
````
$sudo docker build --tag redis-alpine:3.10.3 --build-arg alpine_ver=3.10.3 --build-arg  redis_ver=6.0.9 -f build/alpine/
$sudo docker push redis-alpine:6.0.9
````

## Using the Redis Image
##### 1. start a single redis node with default redis.conf
````
$sudo docker run -itd --name redis01 -p 6379:6379  redis-ubuntu:6.0.9
$sudo docker run -itd --name redis02 -p 6379:6379  redis-centos:6.0.9
$sudo docker run -itd --name redis03 -p 6379:6379  redis-alpine:6.0.9
````
##### 2. Using this image as base in your own Dockerfile with  custom  redis.conf
````
FROM redis-alpine:6.0.9
COPY redis.conf /opt/redis/conf/redis.conf
CMD [ "/opt/redis/bin/redis-server", "/opt/redis/conf/redis.conf" ]
````

#### 3. Using custom redis.conf file from docker cli 
````
$ docker run -itd -v /mylocal/conf/redis.conf:/opt/redis/conf/redis.conf -p 6379:6379 --name redis-test01 redis-centos:6.0.9
````

#### 4. Using the redis-cli from the image
````
$ docker run -it redis-alpine:6.0.9 /opt/redis/bin/redis-cli -h redis-test01:6379
````

#### 5. Start redis with custom redis.conf and persistent storage
````
$sudo docker run -d --name redis-test-01  \
                     -p 6379:6379 \
                     -v /local/redis/conf/redis.conf:/opt/redis/conf/redis.conf \
                     -v /local/redis/data/:/opt/redis/data/ \
                     redis-ubuntu:6.0.9
````                     



