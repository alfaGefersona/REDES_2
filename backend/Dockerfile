FROM debian:bullseye

ENV ASTERISK_VERSION=18

# Dependências
RUN apt-get update && apt-get install -y \
    build-essential git wget curl subversion uuid-dev \
    libjansson-dev libxml2-dev libsqlite3-dev libedit-dev \
    libsrtp2-dev libssl-dev pkg-config \
    libncurses5-dev libspeex-dev libvorbis-dev \
    libcurl4-openssl-dev libogg-dev \
    && rm -rf /var/lib/apt/lists/*

# Clona e compila o Asterisk
WORKDIR /usr/src
RUN git clone -b ${ASTERISK_VERSION} https://github.com/asterisk/asterisk.git && \
    cd asterisk && \
    contrib/scripts/get_mp3_source.sh && \
    ./configure --with-srtp --with-pjproject-bundled && \
    make menuselect.makeopts && \
    menuselect/menuselect \
        --enable res_http_websocket \
        --enable res_srtp \
        --enable codec_opus \
        --enable format_mp3 \
        menuselect.makeopts && \
    make -j$(nproc) && \
    make install && \
    make samples && \
    make config

# Porta SIP e WebSocket
EXPOSE 5060/udp 8088/tcp

# Copia configs externas (você pode montar via volume)
VOLUME /etc/asterisk

# Executa o Asterisk
CMD ["/usr/sbin/asterisk", "-f", "-vvv"]
