# centos base version as build argument
ARG centos_ver=7
FROM centos:${centos_ver} as base

# redis stable version to be downloaded
ARG redis_ver=5.0.5

# metadata and environments variables defined  last for better caching
# environment variables
ENV REDIS_VERSION=${redis_ver} \
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
    && wget http://download.redis.io/releases/redis-${redis_ver}.tar.gz \
    && tar xvzf redis-${redis_ver}.tar.gz \
    && mkdir -p /opt/redis/conf /opt/redis/logs/ opt/redis/data /opt/redis/utils 

RUN set -x \
    && cd /tmp/redis-${redis_ver} \
    && make deps | tee /opt/redis/logs/make_deps.log \
    && make      | tee /opt/redis/logs/make_redis.log \
    && make test | tee /opt/redis/logs/make_tests.log \
    && make PREFIX=${REDIS_HOME} install \
    && cp redis.conf /opt/redis/conf/redis.conf \
    && cp sentinel.conf /opt/redis/conf/sentinel.conf \
    && cp runtest* /opt/redis/utils/ 

# Final minimal image 
FROM centos:${centos_ver}

# Image meta data
LABEL   name="Redis" \
        description="OpenSource noSQL key-value database" \
        author="jaggs" \
        email="jrpamid.cloudops@gmail.com"

# metadata and environments variables defined  last for better caching
# # environment variables
ENV REDIS_VERSION=${redis_ver} \
    REDIS_USER=redis \
    REDIS_HOME=/opt/redis \
    REDIS_PORT=6379 

# copy the compiled binaries from the previous stage
COPY --from=base /opt/ /opt/

RUN set -x \
    && yum updateinfo \
    && yum install -y tcl \
    && useradd  --comment "Redis user" \
                 --home-dir /home/redis \
                 --system \ 
                 --user-group ${REDIS_USER} \ 
    && chmod -R 755 /opt/redis/* \
    && chown -R ${REDIS_USER}:${REDIS_USER} /opt/redis/* \
    && yum clean all  

EXPOSE ${REDIS_PORT}

USER ${REDIS_USER}

CMD ["/opt/redis/bin/redis-server", "/opt/redis/conf/redis.conf"]



