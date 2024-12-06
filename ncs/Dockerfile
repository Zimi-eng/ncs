FROM debian:stable-slim

ARG NCS_SDK_VERSION=v2.5.0

# OS dependencies and packages
RUN \
  apt-get -y update \
  && apt-get -y install --no-install-recommends \
  ca-certificates \
  python3 \
  python3-pip \
  git \
  wget \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Install west
RUN pip3 install west

# NCS SDK installation
RUN \
  wget https://developer.nordicsemi.com/.pc-tools/nrfutil/x64-linux/nrfutil \
  && chmod +x nrfutil \
  && mv nrfutil /usr/bin \
  && nrfutil install toolchain-manager \
  && nrfutil toolchain-manager install --ncs-version ${NCS_SDK_VERSION} \
  && apt-get remove -y --purge \
  wget \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf ncs/downloads

# Environment setup
ENV ZEPHYR_BASE=/opt/ncs/nrf/zephyr
ENV PATH=$ZEPHYR_BASE/scripts:$PATH

WORKDIR /workspace
