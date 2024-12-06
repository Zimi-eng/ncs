FROM debian:stable-slim

ARG NCS_SDK_VERSION=v2.5.0

# OS dependencies and packages
RUN \
  apt-get -y update \
  && apt-get -y install --no-install-recommends \
  ca-certificates \
  python3 \
  python3-pip \
  python3-venv \
  git \
  wget \
  ccache \  # Install ccache
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Create and use a virtual environment
RUN python3 -m venv /opt/west_env \
  && /opt/west_env/bin/pip install west

# Add the virtual environment to PATH
ENV PATH="/opt/west_env/bin:$PATH"

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
