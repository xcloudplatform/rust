FROM ubuntu:22.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    g++ \
    make \
    ninja-build \
    file \
    curl \
    ca-certificates \
    python3 \
    git \
    cmake \
    sudo \
    gdb \
    libssl-dev \
    pkg-config \
    xz-utils

ENV RUSTUP_INIT_SKIP_PATH_CHECK="yes"
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y

RUN PATH="${HOME}/.cargo/bin:${PATH}" \
    cargo install --git https://github.com/solana-labs/cargo-run-solana-tests.git \
    --rev 12186c99173cc30771897932159b53acca836321 \
    --bin cargo-run-solana-tests --root /usr/local

COPY scripts/sccache.sh /scripts/
RUN sh /scripts/sccache.sh

# We are disabling CI LLVM since this builder is intentionally using a host
# LLVM, rather than the typical src/llvm-project LLVM.
ENV NO_DOWNLOAD_CI_LLVM 1

ENV RUST_CONFIGURE_ARGS \
    --set rust.lld \
    --set llvm.clang

ENV SCRIPT CARGO_TARGET_SBF_SOLANA_SOLANA_RUNNER=\"cargo-run-solana-tests --heap-size 104857600\" \
    LLVM_HOME=/checkout/obj/build/x86_64-unknown-linux-gnu/llvm \
    PATH="${HOME}/.cargo/bin:${PATH}" \
    python3 /checkout/x.py --stage 1 test --host='' --target sbf-solana-solana \
    library/core
