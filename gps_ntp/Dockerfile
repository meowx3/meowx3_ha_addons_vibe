ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23
FROM ${BUILD_FROM}

ARG TARGETARCH
RUN \
    if [ -z "${TARGETARCH}" ]; then \
        echo "TARGETARCH is not set, please use Docker BuildKit for the build." && exit 1; \
    fi \
    && case "${TARGETARCH}" in \
        amd64|arm64) ;; \
        *) echo "Unsupported TARGETARCH: ${TARGETARCH}" && exit 1 ;; \
    esac \
    && apk add --no-cache chrony gpsd gpsd-clients python3

COPY rootfs /

LABEL \
    org.opencontainers.image.title="Home Assistant App: GPS NTP Server" \
    org.opencontainers.image.description="USB GNSS and Chrony NTP server with satellite sky view" \
    org.opencontainers.image.source="https://github.com/meowx3/meowx3_ha_addons_vibe" \
    org.opencontainers.image.licenses="Apache License 2.0"
