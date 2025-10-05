FROM debian:bookworm-slim

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    ca-certificates \
    rsync \
    unzip \
    wget \
    lib32z1 \
    locales \
    libicu-dev \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && rm -rf /var/lib/apt/lists/* \
    && useradd -m cs2 \
    && mkdir -p /usr/cs2 \
    && chown cs2:cs2 /usr/cs2

USER cs2

RUN mkdir /home/cs2/Steam && \
    mkdir /home/cs2/server

WORKDIR /home/cs2/Steam

RUN wget -qO- https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz | tar zxf - \
    && /home/cs2/Steam/steamcmd.sh +quit \
    && mkdir -p /home/cs2/.steam/sdk64 \
    && ln -s /home/cs2/Steam/linux64/steamclient.so /home/cs2/.steam/sdk64/steamclient.so

WORKDIR /home/cs2

ENV LD_LIBRARY_PATH="/home/cs2/server/game/bin/linuxsteamrt64:${LD_LIBRARY_PATH}"

COPY server.sh .

LABEL org.opencontainers.image.source=https://github.com/sazarkin/docker-cs2 \
      org.opencontainers.image.title="CS2 Dedicated Server" \
      org.opencontainers.image.description="Docker image for running a Counter-Strike 2 dedicated server." \
      org.opencontainers.image.url="https://github.com/sazarkin/docker-cs2" \
      org.opencontainers.image.licenses="MIT" \
      org.opencontainers.image.vendor="sazarkin" \
      org.opencontainers.image.version="1.0.0"

ENTRYPOINT ["/home/cs2/server.sh"]
