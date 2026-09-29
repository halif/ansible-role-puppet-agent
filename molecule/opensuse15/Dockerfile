FROM opensuse/leap:15.6

ENV container=docker

RUN zypper --non-interactive refresh \
    && zypper --non-interactive install --no-recommends \
        systemd \
        python311 \
        python311-rpm \
        sudo \
        ca-certificates \
    && zypper clean --all

STOPSIGNAL SIGRTMIN+3

CMD ["/sbin/init"]