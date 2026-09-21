FROM rockylinux:9

ENV container=docker
ENV PYTHONDONTWRITEBYTECODE=1

RUN dnf -y update \
    && dnf -y install \
        ansible-core \
        systemd \
        python3 \
        sudo \
        ca-certificates \
        procps-ng \
        iproute \
    && dnf clean all \
    && rm -rf /var/cache/dnf

STOPSIGNAL SIGRTMIN+3

CMD ["/sbin/init"]