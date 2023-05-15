#
#  Dockerfile for building/testing Project MU with GCC.
#
#  Copyright (c) Microsoft Corporation
#  SPDX-License-Identifier: BSD-2-Clause-Patent
#

FROM ubuntu:22.04 AS build

# Versions
ARG GCC_MAJOR_VERSION=12

# Set environment variable to avoid interaction.
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

RUN apt-get update && \
    apt-get install --yes --no-install-recommends \
        acpica-tools \
        ca-certificates \
        git \
        lcov \
        mono-devel \
        nasm \
        python3 \
        python3-pip \
        python3-venv \
        software-properties-common \
        sudo \
        uuid-dev \
        wget \
        && \
    apt-get install --yes --no-install-recommends \
        g++-${GCC_MAJOR_VERSION} gcc-${GCC_MAJOR_VERSION} \
        gcc-${GCC_MAJOR_VERSION}-x86-64-linux-gnux32 \
        gcc-${GCC_MAJOR_VERSION}-aarch64-linux-gnu \
        gcc-${GCC_MAJOR_VERSION}-riscv64-linux-gnu \
        gcc-${GCC_MAJOR_VERSION}-arm-linux-gnueabi \
        gcc-${GCC_MAJOR_VERSION}-arm-linux-gnueabihf \
        && \
    apt-get upgrade -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ENV GCC5_AARCH64_PREFIX /usr/bin/aarch64-linux-gnu-
ENV GCC5_ARM_PREFIX     /usr/bin/arm-linux-gnueabi-
ENV GCC5_RISCV64_PREFIX /usr/bin/riscv64-linux-gnu-

#
# Alternatives
#

RUN update-alternatives \
      --install /usr/bin/python python /usr/bin/python3.10 1 && \
    update-alternatives \
      --install /usr/bin/python3 python3 /usr/bin/python3.10 1 && \
    update-alternatives \
      --install /usr/bin/gcc gcc /usr/bin/gcc-${GCC_MAJOR_VERSION} 100 \
      --slave /usr/bin/g++ g++ /usr/bin/g++-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/gcc-ar gcc-ar /usr/bin/gcc-ar-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/gcc-nm gcc-nm /usr/bin/gcc-nm-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/gcc-ranlib gcc-ranlib /usr/bin/gcc-ranlib-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/gcov gcov /usr/bin/gcov-${GCC_MAJOR_VERSION} && \
    update-alternatives \
      --install /usr/bin/cpp cpp /usr/bin/cpp-${GCC_MAJOR_VERSION} 100 && \
    update-alternatives \
      --install /usr/bin/aarch64-linux-gnu-gcc aarch64-linux-gnu-gcc /usr/bin/aarch64-linux-gnu-gcc-${GCC_MAJOR_VERSION} 100 \
      --slave /usr/bin/aarch64-linux-gnu-g++ aarch64-linux-gnu-g++ /usr/bin/aarch64-linux-gnu-g++-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/aarch64-linux-gnu-gcc-ar aarch64-linux-gnu-gcc-ar /usr/bin/aarch64-linux-gnu-gcc-ar-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/aarch64-linux-gnu-gcc-nm aarch64-linux-gnu-gcc-nm /usr/bin/aarch64-linux-gnu-gcc-nm-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/aarch64-linux-gnu-gcc-ranlib aarch64-linux-gnu-gcc-ranlib /usr/bin/aarch64-linux-gnu-gcc-ranlib-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/aarch64-linux-gnu-gcov aarch64-linux-gnu-gcov /usr/bin/aarch64-linux-gnu-gcov-${GCC_MAJOR_VERSION} && \
    update-alternatives \
      --install /usr/bin/arm-linux-gnueabi-gcc arm-linux-gnueabi-gcc /usr/bin/arm-linux-gnueabi-gcc-${GCC_MAJOR_VERSION} 100 \
      --slave /usr/bin/arm-linux-gnueabi-g++ arm-linux-gnueabi-g++ /usr/bin/arm-linux-gnueabi-g++-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/arm-linux-gnueabi-gcc-ar arm-linux-gnueabi-gcc-ar /usr/bin/arm-linux-gnueabi-gcc-ar-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/arm-linux-gnueabi-gcc-nm arm-linux-gnueabi-gcc-nm /usr/bin/arm-linux-gnueabi-gcc-nm-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/arm-linux-gnueabi-gcc-ranlib arm-linux-gnueabi-gcc-ranlib /usr/bin/arm-linux-gnueabi-gcc-ranlib-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/arm-linux-gnueabi-gcov arm-linux-gnueabi-gcov /usr/bin/arm-linux-gnueabi-gcov-${GCC_MAJOR_VERSION} && \
    update-alternatives \
      --install /usr/bin/riscv64-linux-gnu-gcc riscv64-linux-gnu-gcc /usr/bin/riscv64-linux-gnu-gcc-${GCC_MAJOR_VERSION} 100 \
      --slave /usr/bin/riscv64-linux-gnu-g++ riscv64-linux-gnu-g++ /usr/bin/riscv64-linux-gnu-g++-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/riscv64-linux-gnu-gcc-ar riscv64-linux-gnu-gcc-ar /usr/bin/riscv64-linux-gnu-gcc-ar-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/riscv64-linux-gnu-gcc-nm riscv64-linux-gnu-gcc-nm /usr/bin/riscv64-linux-gnu-gcc-nm-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/riscv64-linux-gnu-gcc-ranlib riscv64-linux-gnu-gcc-ranlib /usr/bin/riscv64-linux-gnu-gcc-ranlib-${GCC_MAJOR_VERSION} \
      --slave /usr/bin/riscv64-linux-gnu-gcov riscv64-linux-gnu-gcov /usr/bin/riscv64-linux-gnu-gcov-${GCC_MAJOR_VERSION}

FROM build AS test

ARG QEMU_URL="https://gitlab.com/qemu-project/qemu.git"
ARG QEMU_BRANCH="v7.2.0"

RUN apt-get update && apt-get install --yes --no-install-recommends \
        autoconf \
        automake \
        autotools-dev \
        bc \
        build-essential \
        curl \
        gcc \
        libglib2.0-dev \
        libgtk-3-dev \
        libpixman-1-dev \
        libsdl2-dev \
        ninja-build \
        tar \
        && \
    git clone "${QEMU_URL}" --branch "${QEMU_BRANCH}" --depth 1 qemu && \
    cd qemu && \
    curl "https://patchwork.ozlabs.org/project/qemu-devel/patch/20230105161804.82486-1-lersek@redhat.com/mbox/" | git apply --ignore-whitespace && \
    ./configure --target-list=x86_64-softmmu,aarch64-softmmu --enable-gtk && \
    make install -j $(nproc) && \
    cd .. && \
    rm -rf qemu && \
    apt remove --yes ninja-build && \
    apt-get clean

FROM test AS dev

RUN apt-get update && \
    apt-get install --yes --no-install-recommends \
        nano \
        less \
        && \
    apt-get clean
