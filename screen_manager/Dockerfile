FROM ghcr.io/ihost-open-source-project/esphome-hassio:2025.6.3
ARG BUILD_VERSION
ARG BUILD_ARCH
ARG TARGETARCH
LABEL io.hass.version="${BUILD_VERSION}" io.hass.type="app" io.hass.arch="${BUILD_ARCH:-$TARGETARCH}"

RUN pip install --no-cache-dir --break-system-packages 'aiohttp>=3.12,<4' PyYAML

COPY app /app
COPY translations /app/translations
COPY CHANGELOG.md /app/CHANGELOG.md
WORKDIR /app

HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
  CMD curl --fail --silent --output /dev/null http://127.0.0.1:8099/health || exit 1

ARG CHANNEL=""
ENV SCREEN_CHANNEL="${CHANNEL}"
ENTRYPOINT []
CMD ["python3", "/app/server.py"]
