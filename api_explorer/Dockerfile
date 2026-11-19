ARG BUILD_FROM=ghcr.io/home-assistant/base-python:3.14-alpine3.23
FROM ${BUILD_FROM}

# Renovate updates this version (rule in .github/renovate.json)
RUN pip3 install --no-cache-dir aiohttp==3.13.5

COPY rootfs /
