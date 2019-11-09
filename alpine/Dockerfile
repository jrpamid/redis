# alpine_ver build argument
ARG alpine_ver
FROM alpine:${alpine_ver}

# Image meta data
LABEL   name="Redis" \
        description="OpenSource noSQL key-value database" \
        author="jaggs" \
        email="jrpamid.cloudops@gmail.com"


# metadata and environments variables
ENV REDIS_VERSION=${redis_ver} \
    REDIS_USER=redis \
    REDIS_HOME=/opt/redis \
    REDIS_PORT=6379


# install the source building dependencies
RUN set -x \
    &&  apk add --no-cache --virtual .build-deps \
                build-base \
                tar \
                gcc \
                gcc-dev \
                linux-headers \
                tcl \
                tzdata

# redis stable version to be downloaded
ARG redis_ver=5.0.5

# download the source, compile and test 
# compile the redis/deps{jemalloc,lua,hiredis} first
# cleanup after the install
RUN set -x \
    && cd /tmp \
    && wget http://download.redis.io/releases/redis-${redis_ver}.tar.gz \
    && tar xvzf reis-${redis_ver}.tar.gz 

RUN set -x \
    && cd /tmp/redis-${redis_ver} \
    && make deps \
    && make  \
    && make test \
    && make PREFIX=${REDIS_HOME} install \
    && mkdir -p /opt/redis/{conf,logs,data} \
    && cp redis.conf /opt/redis/conf \
    && cp sentinel.conf /opt/redis/conf \
    && apk remove .build-deps \
    && rm -Rf /tmp/* 



RUN set -x \
    && adduser -S -G ${REDIS_USER} -h /home/redis/  ${REDIS_USER} \
    && chmod -R 750 /opt/redis \
    && chown -R ${REDIS_USER}:${REDIS_USER} /opt/redis

EXPOSE ${REDIS_PORT}

USER ${REDIS_USER}

CMD ["/opt/redis/bin/redis-server", "/opt/redis/conf/redis.conf"]

