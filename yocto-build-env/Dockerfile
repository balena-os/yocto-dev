# yocto build environment, forked from
# https://github.com/balena-os/balena-yocto-scripts/blob/45e32821ac6e3efba81e24a21e417a375da5e154/automation/Dockerfile_yocto-build-env

FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

# Install the following utilities (required by poky)
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    build-essential chrpath curl diffstat gcc-multilib gawk git-core locales zstd liblz4-tool \
    texinfo unzip wget xterm cpio file python python3 openssh-client iputils-ping iproute2 \
    python3-distutils python3-pip python3-pexpect python3-git python3-jinja2 python3-subunit \
    gawk socat xz-utils libegl1-mesa libsdl1.2-dev pylint3 mesa-common-dev debianutils \
    && rm -rf /var/lib/apt/lists/*

# Set the locale to UTF-8 for bulding with poky morty
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8

# Additional host packages required by balena
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends apt-transport-https ca-certificates curl git gnupg lsb-release sudo uidmap \
    && rm -rf /var/lib/apt/lists/*

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN curl --silent https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add -

ARG NODE_VERSION=node_8.x

RUN echo "deb https://deb.nodesource.com/$NODE_VERSION $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/nodesource.list \
    && echo "deb-src https://deb.nodesource.com/$NODE_VERSION $(lsb_release -cs) main" | tee -a /etc/apt/sources.list.d/nodesource.list

# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends jq nodejs \
    && rm -rf /var/lib/apt/lists/*

# Additional host packages required by various BSP layers
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends dos2unix \
    && rm -rf /var/lib/apt/lists/*

# Install docker matching the balena-engine version
# https://docs.docker.com/engine/install/ubuntu/
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends iptables procps e2fsprogs xfsprogs xz-utils kmod \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

ARG DOCKER_VERSION="5:19.03.13~3-0~ubuntu-bionic"

# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install -y --no-install-recommends docker-ce=${DOCKER_VERSION} docker-ce-cli=${DOCKER_VERSION} containerd.io \
    && rm -rf /var/lib/apt/lists/*

VOLUME /var/lib/docker

# Install balena-cli
# renovate: datasource=github-releases depName=balena-io/balena-cli
ARG BALENA_CLI_VERSION=v14.5.15

RUN curl -fsSL https://github.com/balena-io/balena-cli/releases/download/$BALENA_CLI_VERSION/balena-cli-$BALENA_CLI_VERSION-linux-x64-standalone.zip > balena-cli.zip && \
    unzip balena-cli.zip \
    && mv balena-cli/* /usr/bin \
    && rm -rf balena-cli.zip balena-cli

WORKDIR /work

CMD [ "sleep", "infinity" ]
