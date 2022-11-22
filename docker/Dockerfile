FROM debian:11.5-slim AS build
RUN apt-get update && apt-get install -y \
        libssl-dev \
        libconfig-dev \
        libnice-dev  \
        libnice-dev \
        libsrtp2-dev \
        libopus-dev \
        libogg-dev \
        libcurl4-openssl-dev \
        libmicrohttpd-dev \
        libjansson-dev \
        libwebsockets-dev \
        cmake \
        git
WORKDIR /janus-build/
COPY . /janus-build

RUN ["chmod", "+x", "configure"]
RUN ["./configure", "--prefix=/usr/local/janus", "--disable-aes-gcm"]

RUN mkdir -p /usr/local/janus

RUN ["make", "clean"]
RUN ["make"]
RUN ["make", "install"]


FROM debian:11.5-slim
RUN apt-get update && apt-get install -y \
        libconfig-dev \
        libnice-dev \
        libjansson-dev \
        libsrtp2-dev \
        libcurl4-openssl-dev \
        libmicrohttpd-dev \
        libwebsockets-dev \
        libssl-dev \
        libogg-dev \
        tini 
WORKDIR /janus

RUN ["useradd", "janus"]
COPY --from=build --chown=janus /usr/local/janus /usr/local/janus
COPY --from=build --chown=janus /janus-build/config /usr/local/janus/etc/janus

COPY --from=build --chown=janus /janus-build/docker/start.sh /janus/

USER janus
ENTRYPOINT ["tini", "--", "./start.sh"]