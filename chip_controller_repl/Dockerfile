ARG BUILD_FROM=ghcr.io/home-assistant/base-debian:trixie
FROM $BUILD_FROM AS builder-base

# hadolint ignore=DL3009
RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
       git \
       patch \
       gcc g++ \
       make \
       pkg-config

WORKDIR /usr/src


FROM builder-base AS builder-python-dbus-wheel

RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
       build-essential \
       python3-dev \
       python3-pip \
       dbus \
       libdbus-1-dev \
       libglib2.0-dev \
    && pip3 wheel dbus-python==1.2.18 \
    && pip3 wheel psutil==5.9.5


FROM builder-base AS builder-ttyd

ARG TTYD_VERSION=1.7.7

RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
       cmake \
       libuv1-dev \
       libc-dev \
       libssl-dev \
       zlib1g-dev \
       libjson-c-dev \
       libwebsockets-dev \
       libwebsockets-evlib-uv \
    && cd /usr/src \
    && git clone --branch main --single-branch \
        https://github.com/tsl0922/ttyd \
    && cd ttyd \
    && git checkout "${TTYD_VERSION}" \
    && mkdir -p build \
    && cd build \
    && cmake .. \
        -DCMAKE_BUILD_TYPE=MinSizeRel \
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        -DCMAKE_VERBOSE_MAKEFILE=TRUE \
    && make \
    && make DESTDIR=./install install


FROM $BUILD_FROM

RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
       libwebsockets-evlib-uv \
       openssl \
       zlib1g \
       libjson-c5 \
       python3-venv \
       python3-pip \
       python3-gi \
       python3-gi-cairo \
       python3-construct \
       unzip \
       libcairo2 \
       dbus \
       libdbus-1-3 \
       libglib2.0-0 \
       gdb \
       git \
    && git clone --depth 1 -b v1.0-branch \
       https://github.com/project-chip/connectedhomeip \
    && cp -r connectedhomeip/credentials /root/credentials \
    && rm -rf connectedhomeip \
    && apt-get purge -y --auto-remove \
       git \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /usr/src/*

COPY --from=builder-ttyd /usr/src/ttyd/build/install /

WORKDIR /root

ARG HOME_ASSISTANT_CHIP_VERSION=2023.5.2

# hadolint ignore=DL3013
RUN \
    --mount=type=bind,from=builder-python-dbus-wheel,source=/usr/src,target=/tmp/wheels \
    pip3 install --no-cache-dir --break-system-packages \
       /tmp/wheels/dbus_python-1.2.18-*.whl \
       /tmp/wheels/psutil-5.9.5-*.whl \
    && pip3 install --no-cache-dir --break-system-packages \
       home-assistant-chip-clusters==${HOME_ASSISTANT_CHIP_VERSION} \
       home-assistant-chip-core==${HOME_ASSISTANT_CHIP_VERSION} \
       home-assistant-chip-repl==${HOME_ASSISTANT_CHIP_VERSION} \
    && pip3 install --break-system-packages --no-cache-dir jupyter-console

COPY rootfs /
