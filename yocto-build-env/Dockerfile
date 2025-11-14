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
ENV LANG=en_US.UTF-8

# Additional host packages required by balena
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends apt-transport-https ca-certificates curl git gnupg lsb-release openssh-server sudo uidmap \
    && rm -rf /var/lib/apt/lists/*

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# hadolint ignore=DL3008,DL3015
RUN apt-get update \
    && apt-get install -y --no-install-recommends jq nodejs npm sudo \
    && rm -rf /var/lib/apt/lists/*

# Additional host packages required by various BSP layers
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends dos2unix \
    && rm -rf /var/lib/apt/lists/*

# Add keyring for docker-ce
# https://docs.docker.com/engine/install/ubuntu/
RUN mkdir -p /etc/apt/keyrings \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
    && chmod a+r /etc/apt/keyrings/docker.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list

# renovate: datasource=github-releases depName=docker/cli extractVersion=^v(?<version>.*)$
ARG DOCKER_VERSION=20.10.24

# renovate: datasource=github-releases depName=docker/compose extractVersion=^v(?<version>.*)$
ARG DOCKER_COMPOSE_VERSION=2.40.3

# renovate: datasource=github-releases depName=docker/buildx extractVersion=^v(?<version>.*)$
ARG DOCKER_BUILDX_VERSION=0.29.1

# Install Docker
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        docker-ce=5:${DOCKER_VERSION}* \
        docker-ce-cli=5:${DOCKER_VERSION}* \
        containerd.io \
        docker-buildx-plugin=${DOCKER_BUILDX_VERSION}* \
        docker-compose-plugin=${DOCKER_COMPOSE_VERSION}* \
    && rm -rf /var/lib/apt/lists/*

# Install additional utilities
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends iptables procps e2fsprogs xfsprogs xz-utils git kmod \
    && rm -rf /var/lib/apt/lists/*

VOLUME /var/lib/docker

# Install balena-cli
# renovate: datasource=github-releases depName=balena-io/balena-cli
ARG BALENA_CLI_VERSION=v22.5.5

RUN curl -fsSL https://github.com/balena-io/balena-cli/releases/download/${BALENA_CLI_VERSION}/balena-cli-${BALENA_CLI_VERSION}-linux-x64-standalone.tar.gz | tar -xzf - && \
    cp -a balena /usr/lib/balena && \
    ln -sf /usr/lib/balena/bin/balena /usr/bin/balena && \
    balena version

# renovate: datasource=github-releases depName=just-containers/s6-overlay
ARG S6_OVERLAY_VERSION=3.1.4.2

ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-noarch.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-noarch.tar.xz
ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-x86_64.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-x86_64.tar.xz

ENV S6_KEEP_ENV=1
ENV S6_READ_ONLY_ROOT=1
ENV S6_CMD_WAIT_FOR_SERVICES_MAXTIME=0

# install s6-overlay
COPY s6-overlay /etc/s6-overlay
RUN chmod +x /etc/s6-overlay/scripts/*

# allow sudo without password
RUN echo "%sudo   ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers \
    && echo "Defaults env_keep += \"DEBIAN_FRONTEND\"" >> /etc/sudoers

VOLUME /home
VOLUME /etc/ssh

ENTRYPOINT [ "/init" ]
