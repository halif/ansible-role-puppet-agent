FROM ubuntu:22.04

ENV container=docker
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y \
        ansible \
        systemd \
        systemd-sysv \
        python3 \
        python3-apt \
        sudo \
        ca-certificates \
        curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

STOPSIGNAL SIGRTMIN+3

CMD ["/lib/systemd/systemd"]
