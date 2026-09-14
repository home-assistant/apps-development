ARG BUILD_FROM=ghcr.io/home-assistant/base-debian:trixie
ARG BUILD_ARCH

ARG CHIP_VERSION=v1.6.0.0

# Build ttyd

FROM $BUILD_FROM AS builder-ttyd

ARG TTYD_VERSION=1.7.7

WORKDIR /usr/src

RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
       git \
       gcc g++ \
       make \
       cmake \
       pkg-config \
       libuv1-dev \
       libc-dev \
       libssl-dev \
       zlib1g-dev \
       libjson-c-dev \
       libwebsockets-dev \
       libwebsockets-evlib-uv \
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

# Download example apps and credentials

FROM $BUILD_FROM AS builder-downloads

ARG BUILD_ARCH
ARG CHIP_VERSION

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ADD https://github.com/project-chip/connectedhomeip/archive/${CHIP_VERSION}.tar.gz /tmp/chip.tar.gz

RUN \
    mkdir /credentials \
    && tar -xzf /tmp/chip.tar.gz --strip-components=2 --directory=/credentials --wildcards '*/credentials/*' \
    && rm /tmp/chip.tar.gz

WORKDIR /examples

RUN \
    set -x \
    && export REPO_OWNER="agners" \
    && export REPO_NAME="matter-linux-example-apps" \
    && if [ "${BUILD_ARCH}" = "amd64" ]; then \
            export EXAMPLE_ARCH="x86-64"; \
       elif [ "${BUILD_ARCH}" = "aarch64" ]; then \
            export EXAMPLE_ARCH="aarch64"; \
       else \
            exit 1; \
       fi \
    && export API_URL="https://api.github.com/repos/$REPO_OWNER/$REPO_NAME/releases/tags/${CHIP_VERSION}" \
    && curl -s $API_URL | \
       jq -r --arg suffix "-${EXAMPLE_ARCH}" '.assets[] | select(.name | endswith($suffix)) | .browser_download_url, (.name | rtrimstr($suffix))' | \
       while read -r url; read -r name; do \
         echo "Downloading $url..."; \
         curl -Lo "$name" "$url"; \
         chmod +x "$name"; \
       done

# Build final image

FROM $BUILD_FROM

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
       libwebsockets-evlib-uv \
       openssl \
       zlib1g \
       libjson-c5 \
       libdbus-1-3 \
       libglib2.0 \
       libnl-3-200 \
       libnl-route-3-200 \
       tmux \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /usr/src/*

COPY --from=builder-ttyd /usr/src/ttyd/build/install /
COPY --from=builder-downloads /examples/ /usr/local/bin/
COPY --from=builder-downloads /credentials /root/credentials

WORKDIR /root

COPY rootfs /
