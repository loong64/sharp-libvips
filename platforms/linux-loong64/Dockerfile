FROM debian:trixie
LABEL maintainer="Lovell Fuller <npm@lovell.info>"

## Copyright 2017 Lovell Fuller and others.
## SPDX-License-Identifier: Apache-2.0

# Create Debian 13 (glibc 2.41) container suitable for cross-compiling Linux LoongArch 64-bit binaries

# Path settings
ENV \
  RUSTUP_HOME="/usr/local/rustup" \
  CARGO_HOME="/usr/local/cargo" \
  PATH="/usr/local/cargo/bin:/root/.local/bin:$PATH"

COPY trixie-loong64.sources /etc/apt/sources.list.d/trixie-loong64.sources
RUN \
  dpkg --add-architecture loong64 && \
  apt-get update -o Acquire::AllowInsecureRepositories=true && \
  apt-get install -y --allow-unauthenticated curl debian-loong64-non-official-archive-keyring && \
  sed -i 's/# Signed-By/Signed-By/g' /etc/apt/sources.list.d/trixie-loong64.sources

# Build dependencies
RUN \
  apt-get update && \
  apt-get install -y \
    autoconf \
    autopoint \
    cmake \
    g++-loongarch64-linux-gnu \
    gettext \
    git \
    gperf \
    jq \
    libcurl4-openssl-dev \
    libssl-dev \
    libtool \
    ninja-build \
    pipx \
    pkgconf \
    pkgconf:loong64 \
    && \
  curl https://sh.rustup.rs -sSf | sh -s -- -y \
    --no-modify-path \
    --profile minimal \
    --target loongarch64-unknown-linux-gnu \
    --default-toolchain nightly \
    && \
  cargo install cargo-c --locked && \
  pipx install meson

# Compiler settings
ENV \
  PKG_CONFIG="loongarch64-linux-gnu-pkg-config --static" \
  PLATFORM="linux-loong64" \
  CHOST="loongarch64-linux-gnu" \
  RUST_TARGET="loongarch64-unknown-linux-gnu" \
  FLAGS="" \
  RUSTFLAGS="-Zlocation-detail=none -Zfmt-debug=none" \
  MESON="--cross-file=/root/meson.ini"

COPY Toolchain.cmake /root/
COPY meson.ini /root/
