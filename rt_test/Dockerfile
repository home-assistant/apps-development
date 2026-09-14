ARG BUILD_FROM=ghcr.io/home-assistant/base:3.24
FROM $BUILD_FROM AS builder

# Setup base

WORKDIR /usr/src

RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base \
        cmake \
        paho-mqtt-c-dev

# Build mqtt-rt-test

COPY build /usr/src/mqtt-rt-test/

RUN \
    set -x \
    && cd /usr/src/mqtt-rt-test \
    && mkdir -p build \
    && cd build \
    && cmake .. \
    && make

# Combine binaries for the app image

FROM $BUILD_FROM

RUN \
    set -x \ 
    && apk add --no-cache \
        bash-completion \
        mosquitto-clients \
        paho-mqtt-c \
        tmux \
        ttyd \
        vim \
    \
    && sed -i "s/ash/bash/" /etc/passwd

COPY --from=builder /usr/src/mqtt-rt-test/build/mqtt-rt-test /usr/local/bin/

WORKDIR /root

# Copy data
COPY rootfs /
