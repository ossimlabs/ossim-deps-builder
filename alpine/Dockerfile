FROM nexus-docker-public-group.ossim.io/alpine:latest

ENV OSSIM_DEV_HOME=/work \
    OSSIM_HOME=/work/ossim \
    OSSIM_BUILD_DIR=/work/build \
    OSSIM_VERSION=1.9.0 \
    OSSIM_VERSION_TAG=SNAPSHOT \
    REPOSITORY_MANAGER_URL=https://nexus.ossim.io/repository \
    OSSIM_PREFS_FILE=/usr/local/share/ossim/ossim-site-preferences \
    OSSIM_INSTALL_PREFIX=/usr/local \
    OSSIM_DATA=/data \
    PATH=/usr/local/bin:/usr/bin:$PATH \
    LD_LIBRARY_PATH=/usr/local/lib:/usr/local/lib64:$PATH \
    JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories  \
    && apk update \
    && apk add --update git cmake make autoconf automake libtool g++ bash linux-headers tiff-dev jpeg-dev \
                libpng-dev swig curl-dev ffmpeg-dev sqlite sqlite-dev python3-dev curl unzip zip \
                openjdk8 apache-ant \
        apk upgrade cmake