# yocto build environment, adapted from
# https://github.com/balena-os/balena-yocto-scripts/blob/a880342e40fe05d00a0f1cf2582a0699921152e0/automation/Dockerfile_yocto-build-env

FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install the following utilities (required by poky)
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    build-essential chrpath curl diffstat gcc-multilib gawk git-core locales zstd liblz4-tool \
    texinfo unzip wget xterm cpio file python3 openssh-client iputils-ping iproute2 \
    python3-distutils python3-pip python3-pexpect python3-git python3-jinja2 python3-subunit \
    gawk socat xz-utils libegl1-mesa libsdl1.2-dev pylint mesa-common-dev debianutils vim sharutils rsync \
    mc byobu bash-completion pigz less nano \
    && rm -rf /var/lib/apt/lists/*

# Set the locale to UTF-8 for bulding with poky morty
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8

# Additional host packages required by balena
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends apt-transport-https ca-certificates curl git gnupg lsb-release openssh-server sudo uidmap \
    && rm -rf /var/lib/apt/lists/*

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# hadolint ignore=DL3008,DL3015
RUN apt-get update && apt-get install -y jq nodejs npm sudo && rm -rf /var/lib/apt/lists/*

# Additional host packages required by various BSP layers
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends dos2unix \
    && rm -rf /var/lib/apt/lists/*

# Install docker matching the balena-engine version
# https://docs.docker.com/engine/install/ubuntu/
# hadolint ignore=DL3008,DL3015
RUN apt-get update && apt-get install -y iptables procps e2fsprogs xfsprogs xz-utils git kmod apt-transport-https ca-certificates curl gnupg lsb-release docker.io && rm -rf /var/lib/apt/lists/*

VOLUME /var/lib/docker

# Install balena-cli
# renovate: datasource=github-releases depName=balena-io/balena-cli
ARG BALENA_CLI_VERSION=v21.1.1

RUN curl -fsSL https://github.com/balena-io/balena-cli/releases/download/$BALENA_CLI_VERSION/balena-cli-$BALENA_CLI_VERSION-linux-x64-standalone.zip > balena-cli.zip && \
    unzip balena-cli.zip \
    && mv balena-cli/* /usr/bin \
    && rm -rf balena-cli.zip balena-cli

# renovate: datasource=github-releases depName=just-containers/s6-overlay
ARG S6_OVERLAY_VERSION=3.1.4.2

ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-noarch.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-noarch.tar.xz
ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-x86_64.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-x86_64.tar.xz

ENV S6_KEEP_ENV 1
ENV S6_READ_ONLY_ROOT 1
ENV S6_CMD_WAIT_FOR_SERVICES_MAXTIME 0

# install s6-overlay
COPY s6-overlay /etc/s6-overlay
RUN chmod +x /etc/s6-overlay/scripts/*

# allow sudo without password
RUN echo "%sudo   ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers \
    && echo "Defaults env_keep += \"DEBIAN_FRONTEND\"" >> /etc/sudoers

VOLUME /home
VOLUME /etc/ssh

ENTRYPOINT [ "/init" ]
