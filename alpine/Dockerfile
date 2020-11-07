# ALPINE_VER build argument
ARG ALPINE_VER=latest
FROM alpine:${ALPINE_VER} as base

ARG ALPINE_VER

ENV REDIS_VERSION=${REDIS_VER} \
    REDIS_USER=redis \
    REDIS_HOME=/opt/redis \
    REDIS_PORT=6379 
    
# install the source building dependencies
RUN set -x \
    &&  apk add --no-cache --virtual .build-deps \
                build-base \
                tar \
                gcc \
                linux-headers \
                tcl \
                tzdata 

# redis stable version to be downloaded
ARG REDIS_VER=6.0.9

# download the source, compile and test 
# compile the redis/deps{jemalloc,lua,hiredis} first
# cleanup after the install
RUN set -x \
    && cd /tmp \
    && wget http://download.redis.io/releases/redis-${REDIS_VER}.tar.gz \
    && tar xvzf redis-${REDIS_VER}.tar.gz 

RUN set -x \
    && mkdir -p /opt/redis/logs /opt/redis/bin /opt/redis/conf /opt/redis/data \
    && touch /opt/redis/logs/make_redis.log \
    && cd /tmp/redis-${REDIS_VER} \
    && make deps | tee /opt/redis/logs/make_redis.log \
    && make  	 |tee /opt/redis/logs/make_redis.log \
    && make test -i | tee /opt/redis/logs/make_redis.log \
    && make PREFIX=${REDIS_HOME} install \
    && cp redis.conf /opt/redis/conf \
    && cp sentinel.conf /opt/redis/conf 


RUN set -x \
    && addgroup ${REDIS_USER} \
    && adduser -S -G ${REDIS_USER} -h /home/redis/  ${REDIS_USER} \
    && chmod -R 750 ${REDIS_HOME} \
    && chown -R ${REDIS_USER}:${REDIS_USER} ${REDIS_HOME}


# Minimal Image build stage
FROM alpine:${ALPINE_VER}

# Image meta data
LABEL   name="Redis" \
        description="Redis - OpenSource noSQL key-value database" \
        author="jaggs" \
        email="jrpamid.cloudops@gmail.com" \
        REDIS_HOME=${REDIS_HOME} \
        REDIS_VERSION=${REDIS_VER} \
        redis_port=${REDIS_PORT}

# copy binaries from base build
COPY --from=base /etc/passwd /etc/group /etc/ 
COPY --from=base /opt/ /opt/

# expose the default redis port
EXPOSE ${REDIS_PORT}

# drop from root user 
USER ${REDIS_USER}

CMD ["/opt/redis/bin/redis-server", "/opt/redis/conf/redis.conf"]

