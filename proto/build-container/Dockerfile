# syntax = docker/dockerfile:experimental
FROM ubuntu:focal

# This image was copied from https://github.com/pulumi/pulumi-build-container
# It can probably be simplifed as it is now purely used to generate the protobuf files

LABEL description="Pulumi Protobuf Build Container"

RUN rm -f /etc/apt/apt.conf.d/docker-clean

COPY /scripts/utils.sh /build-scripts/

# Install various packages
COPY /scripts/install-packages.sh /build-scripts/
RUN --mount=target=/var/lib/apt/lists,type=cache \
    --mount=target=/var/cache/apt,type=cache \
    /build-scripts/install-packages.sh

# Install Go compiler
COPY /scripts/install-go.sh /build-scripts/
RUN GOLANG_VERSION=1.22.10 \
    GOLANG_SHA256=736ce492a19d756a92719a6121226087ccd91b652ed5caec40ad6dbfb2252092 \
    /build-scripts/install-go.sh

# Install Node.js and Yarn
COPY /scripts/install-node.sh /build-scripts/
RUN --mount=target=/var/lib/apt/lists,type=cache \
    --mount=target=/var/cache/apt,type=cache \
    NODE_VERSION=18.x \
    NODE_DISTRO=focal \
    YARN_VERSION=3.8.7 \
    /build-scripts/install-node.sh

# Install Python and accoutrements
COPY /scripts/install-python.sh /build-scripts/
RUN --mount=target=/var/lib/apt/lists,type=cache \
    --mount=target=/var/cache/apt,type=cache \
    PYTHON_VERSION=3.9 \
    PIPENV_VERSION=2024.4.0 \
    AWSCLI_VERSION=1.36.19 \
    WHEEL_VERSION=0.45.1 \
    TWINE_VERSION=3.8.0 \
    MYPY_VERSION=3.6.0 \
    /build-scripts/install-python.sh

# Set various required environment variables
ENV GOPATH=/go \
    PATH=/root/.local/bin:/go/bin:/root/.pulumi/bin:/usr/local/go/bin:/root/.yarn/bin/:$PATH

# Install Protocol Buffers compiler and various GRPC generators
COPY /scripts/install-protobuf-tools.sh /build-scripts/
RUN PROTOC_VERSION=29.1 \
    PROTOC_SHA256=00c83fe9722d85e96c81b941b29f17a744b33b4ce66e0f18009fd8937de22c60 \
    PROTOC_GEN_GO_VERSION=1.35.2 \
    PROTOC_GEN_GO_GRPC_VERSION=1.5.1 \
    NODEJS_GRPC_VERSION=1.10.1 \
    NODEJS_GRPC_TOOLS_VERSION=1.12.4 \
    NODEJS_TS_PROTOC_GEN_VERSION=5.3.3 \
    NODEJS_JS_PROTOC_GEN_VERSION=3.21.4 \
    PYTHON_GRPCIO_VERSION=1.68.1 \
    PYTHON_GRPCIO_TOOLS_VERSION=1.68.1 \
    /build-scripts/install-protobuf-tools.sh
