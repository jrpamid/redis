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
--build-arg ubuntu_codename=ubuntu:[disco:xenial:latest]  --build-arg redis_ver=[5.0|5.0.5|5.0.6]
--build-arg centos:[7|latest]  --build-arg redis_ver=[5.0|5.0.5|5.0.6]
--build-arg alpine:[latest:3.10.3] --build-arg redis_ver=[5.0|5.0.5|5.0.6]
````

| ubuntu version | ubuntu_codename  |
| -------------- | ---------------- |
|   19.10        | eoan             |
|   19.04        | disco            |
|   18.04        | bionic           |
|   16.04        | xenial           |
|   14.04        | trusty           |

#### Redis files and directories
|     path/directory           |    Description                     |
| ---------------------------- | ---------------------------------- |
| /opt/redis                   |  REDIS_HOME                        |
| /opt/redis/conf              |  Redis config files (redis.conf)   |
| /opt/redis/logs              |  Redis logs (image build logs)     |
| /opt/redis/data              |  Redis data store area             |
| /opt/redis/utils             |  Redis test-* utilities copied from source code |
## Building the Redis Image
#### 1. redis on ubuntu
````
$sudo docker build --tag redis-ubuntu:5.0.5 --build-arg ubuntu_codename=latest --build-arg redis_ver=5.0.5 -f build/ubuntu/
$sudo docker push redis-ubuntu:5.0.5  
````
#### 2. redis on centos
````
$sudo docker build --tag redis-centos:5.0.6 --build-arg centos_ver:7 --build-arg redis_ver=5.0.6 -f build/centos/
$sudo docker push redis-centos:5.0.6
````
#### 3. redis on alpine
````
$sudo docker build --tag redis-alpine:3.10.3 --build-arg alpine_ver=3.10.3 --build-arg  redis_ver=5.0.5 -f build/alpine/
$sudo docker push redis-alpine:5.0.5
````

## Using the Redis Image
##### 1. start a single redis node with default redis.conf
````
$sudo docker run -itd --name redis01 -p 6379:6379  redis-ubuntu:5.0.6
$sudo docker run -itd --name redis02 -p 6379:6379  redis-centos:5.0.5
$sudo docker run -itd --name redis03 -p 6379:6379  redis-alpine:5.0.5
````
##### 2. Using this image as base in your own Dockerfile with  custom  redis.conf
````
FROM redis-alpine:5.0.5
COPY redis.conf /opt/redis/conf/redis.conf
CMD [ "/opt/redis/bin/redis-server", "/opt/redis/conf/redis.conf" ]
````

#### 3. Using custom redis.conf file from docker cli 
````
$ docker run -itd -v /mylocal/conf/redis.conf:/opt/redis/conf/redis.conf -p 6379"6379 --name redis-test01 redis-centos:5.0.6
````

#### 4. Using the redis-cli from the image
````
$ docker run -it redis-alpine:5.0.5 /opt/redis/bin/redis-cli -h redis-test01:6379
````

#### 5. Start redis with custom redis.conf and persistent storage
````
$sudo docker run -d --name redis-test-01  \
                     -p 6379:6379 \
                     -v /local/redis/conf/redis.conf:/opt/redis/conf/redis.conf \
                     -v /local/redis/data/:/opt/redis/data/ \
                     redis-ubuntu:5.0.5
````                     



