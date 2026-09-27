FROM opensuse/leap:15.6

ENV container=docker

RUN zypper --non-interactive refresh \
    && zypper --non-interactive install --no-recommends \
        systemd \
        python3 \
        python3-xml \
        sudo \
        ca-certificates \
    && zypper clean --all

STOPSIGNAL SIGRTMIN+3

CMD ["/sbin/init"]
