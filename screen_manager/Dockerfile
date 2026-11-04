# Official ESPHome distribution: compiler, ESP-IDF, esptool and OTA CLI included.
FROM ghcr.io/ihost-open-source-project/esphome-hassio:2025.6.3
ARG BUILD_VERSION
ARG BUILD_ARCH
ARG TARGETARCH
LABEL io.hass.version="${BUILD_VERSION}" io.hass.type="app" io.hass.arch="${BUILD_ARCH:-$TARGETARCH}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    pkg-config \
    libavformat-dev libavcodec-dev libavdevice-dev libavutil-dev libswscale-dev libswresample-dev libavfilter-dev \
    libheif-dev \
 && rm -rf /var/lib/apt/lists/*

RUN (pip freeze | grep -iE '^(cryptography|pillow)==' || true) > /tmp/esphome-keeps.txt \
 && pip install --no-cache-dir -c /tmp/esphome-keeps.txt 'aiohttp>=3.12,<4' PyYAML \
      aiortc==1.15.0 av==17.1.0 aioice==0.10.2 pylibsrtp==1.0.0 pyee==14.0.0 pyOpenSSL==26.4.0 google-crc32c==1.9.0 \
      pillow-heif==1.8.0 \
 && rm /tmp/esphome-keeps.txt
COPY app /app
# Every text of the app, the editor and the screens, one file per language (app 0.2.90).
COPY translations /app/translations
# The Update badge shows what's new from the changelog (app 0.2.73).
COPY CHANGELOG.md /app/CHANGELOG.md
WORKDIR /app
# The ESPHome image checks its dashboard on port 6052, which this app never starts, so the container read
# "unhealthy" while everything worked (issue #23). This one asks our own server, on the port the page is on;
# under the Supervisor every other address gets a 403, so it is /health and not /.
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
  CMD curl --fail --silent --output /dev/null http://127.0.0.1:8099/health || exit 1
# The image GitHub builds from dev for Docker is the dev app (docker.yml passes CHANNEL=dev), which an app without a
# Supervisor reads (core.start_channel). Empty everywhere else: under the Supervisor the app's slug says it. Last, so
# dev and main share every layer above.
ARG CHANNEL=""
ENV SCREEN_CHANNEL="${CHANNEL}"
ENTRYPOINT []
CMD ["python3", "/app/server.py"]
