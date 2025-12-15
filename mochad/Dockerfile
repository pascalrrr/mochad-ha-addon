ARG BUILD_FROM
FROM ${BUILD_FROM:-ghcr.io/home-assistant/amd64-base:3.23} AS build

WORKDIR /tmp

# hadolint ignore=DL3018
RUN apk add --update --no-cache \
    git cmake make autoconf automake \
    libusb-dev gcc g++ && \
    rm -rf /var/cache/apk /lib/apk/db && \
    git clone https://github.com/sigmdel/mochad.git
WORKDIR /tmp/mochad
RUN mkdir build-scripts && aclocal && \
    automake --add-missing --copy && autoconf && \
    ./configure && make

FROM ${BUILD_FROM:-ghcr.io/home-assistant/amd64-base:3.23} AS final

COPY --from=build --chmod=0755 /tmp/mochad/mochad /usr/bin/mochad
# hadolint ignore=DL3018
RUN apk add --update --no-cache \
    libusb && \
    rm -rf /var/cache/apk /lib/apk/db

COPY rootfs/ /
RUN chmod +x /etc/s6-overlay/s6-rc.d/*/*

WORKDIR /data

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ARG BUILD_VERSION
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY

LABEL \
    io.hass.name=${BUILD_NAME} \
    io.hass.description=${BUILD_DESCRIPTION} \
    io.hass.arch=${BUILD_ARCH} \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Cayden (Kay) Haun <me@pascalrr.gay>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Pascalrr" \
    org.opencontainers.image.authors="Cayden (Kay) Haun <me@pascalrr.gay>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://pascalrr.gay/hyperion-ha-addon" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}