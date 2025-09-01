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
    && useradd -m cs2

USER cs2

RUN mkdir /home/cs2/Steam && \
    mkdir /home/cs2/server

WORKDIR /home/cs2/Steam

RUN wget -qO- https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz | tar zxf - \
    && /home/cs2/Steam/steamcmd.sh +quit \
    && mkdir -p /home/cs2/.steam/sdk64 \
    && ln -s /home/cs2/Steam/linux64/steamclient.so /home/cs2/.steam/sdk64/steamclient.so

WORKDIR /home/cs2

COPY server.sh .

ENTRYPOINT ["/home/cs2/server.sh"]

