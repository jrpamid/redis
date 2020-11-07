# centos base version as build argument
ARG CENTOS_VER=7
FROM centos:${CENTOS_VER} as base

# redis stable version to be downloaded
ARG REDIS_VER=6.0.9

# metadata and environments variables defined  last for better caching
# environment variables
ENV REDIS_VERSION=${REDIS_VER} \
    REDIS_USER=redis \
    REDIS_HOME=/opt/redis \
    REDIS_PORT=6379 


# install the source building dependencies
RUN set -x \
    && yum update -y  \
    && yum groupinstall -y "Development Tools" \
    && yum install -y wget tar tcl 

# download the source, compile and test 
# compile the redis/deps{jemalloc,lua,hiredis} first
# cleanup after the install
RUN set -x \
    && cd /tmp \
    && wget http://download.redis.io/releases/redis-${REDIS_VER}.tar.gz \
    && tar xvzf redis-${REDIS_VER}.tar.gz \
    && mkdir -p /opt/redis/conf /opt/redis/logs/ opt/redis/data /opt/redis/utils 

RUN set -x \
    && cd /tmp/redis-${REDIS_VER} \
    && make deps | tee /opt/redis/logs/make_deps.log \
    && make      | tee /opt/redis/logs/make_redis.log \
    && make test | tee /opt/redis/logs/make_tests.log \
    && make PREFIX=${REDIS_HOME} install \
    && cp redis.conf /opt/redis/conf/redis.conf \
    && cp sentinel.conf /opt/redis/conf/sentinel.conf 

RUN set -x \
    && useradd  --comment "Redis user" \
                 --home-dir /home/redis \
                 --system \
                 --user-group ${REDIS_USER} \
    && chmod -R 755 ${REDIS_HOME}/* \
    && chown -R ${REDIS_USER}:${REDIS_USER} ${REDIS_HOME}/* 


# Final minimal image 
FROM centos:${CENTOS_VER}

# Image meta data
LABEL   name="Redis" \
        description="OpenSource noSQL key-value database" \
        author="Jagadish Pamidimalla" \
        email="jrpamid.cloudops@gmail.com"

# copy the compiled binaries from the previous stage
# copy the /etc/{passwd|group} from  base 
COPY --from=base  /etc/passwd /etc/group /etc/ 
COPY --from=base /opt/ /opt/

EXPOSE ${REDIS_PORT}

USER ${REDIS_USER}

CMD ["/opt/redis/bin/redis-server", "/opt/redis/conf/redis.conf"]



