FROM ubuntu:22.04

# set noninteractive installation
ENV DEBIAN_FRONTEND=noninteractive \
    NCS_SDK_VERSION=v2.7.0

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \cmake ninja-build gperf \
    ccache dfu-util device-tree-compiler wget \
    python3-dev python3-pip python3-setuptools python3-tk python3-wheel python3-venv xz-utils file \
    make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1 unzip \
    && rm -rf /var/lib/apt/lists/*

# tell cmake where to find the Zephyr SDK cmake packages
ENV CMAKE_PREFIX_PATH /opt/toolchains

# create a Python virtual environment and install Python packages
RUN python3 -m venv /venv \
  && . /venv/bin/activate \
  && pip3 install --no-cache-dir west

# retrieve a lighter version of the Zephyr workspace, which is enough to
# populate a cache for use when building. we'll need to re-generate this image
# if the manifest changes, to keep the benefit.
RUN . /venv/bin/activate \
    && west init -m https://github.com/nrfconnect/sdk-nrf --mr ${NCS_SDK_VERSION} ~/ncs/${NCS_SDK_VERSION} \
    && cd ~/ncs/${NCS_SDK_VERSION} \
    && west update \
    && west zephyr-export \
    && pip install --no-cache-dir -r zephyr/scripts/requirements.txt

# install the Zephyr toolchain only for arm-zephyr-eabi
ENV ZEPHYR_SDK_VERSION=0.16.8
RUN wget -q https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v${ZEPHYR_SDK_VERSION}/zephyr-sdk-${ZEPHYR_SDK_VERSION}_linux-x86_64_minimal.tar.xz \
  && mkdir -p /opt/toolchains \
  && tar xf zephyr-sdk-${ZEPHYR_SDK_VERSION}_linux-x86_64_minimal.tar.xz -C /opt/toolchains \
  && /opt/toolchains/zephyr-sdk-${ZEPHYR_SDK_VERSION}/setup.sh -c -t arm-zephyr-eabi \
  && rm zephyr-sdk-${ZEPHYR_SDK_VERSION}_linux-x86_64_minimal.tar.xz

RUN \
    mkdir ${HOME}/gn \
    && cd ${HOME}/gn \
    && wget -O gn.zip https://chrome-infra-packages.appspot.com/dl/gn/gn/linux-amd64/+/latest \
    && unzip gn.zip \
    && rm gn.zip \
    && ln -s ${HOME}/gn/gn /usr/local/bin/gn

RUN . /venv/bin/activate \
    && pip3 install --no-cache-dir -r ~/ncs/${NCS_SDK_VERSION}/modules/lib/matter/scripts/setup/requirements.nrfconnect.txt

RUN cd ~/ncs/${NCS_SDK_VERSION}/nrf \
    && mkdir zimi \
    && cd zimi

WORKDIR /root/ncs/${NCS_SDK_VERSION}/nrf/zimi

CMD ["/bin/bash", "-c", "source /root/ncs/${NCS_SDK_VERSION}/zephyr/zephyr-env.sh && source /venv/bin/activate && exec bash"]
