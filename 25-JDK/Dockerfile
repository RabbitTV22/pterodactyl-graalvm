
# ----------------------------------
# Pterodactyl Core Dockerfile
# Environment: Java
# Minimum Panel Version: 1.7.0
# ----------------------------------

FROM debian:13-slim

ARG TARGETARCH
ARG GRAAL_VERSION=latest
ARG JAVA_VERSION=25

LABEL maintainer="RabbitTV <contact@rabbittv.ca>"

ENV DEBIAN_FRONTEND=noninteractive

# Default to UTF-8 file.encoding
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# Install dependencies and GraalVM
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        curl \
        ca-certificates \
        openssl \
        git \
        tar \
        sqlite3 \
        fontconfig \
        tzdata \
        locales \
        libnuma-dev \
        iproute2; \
    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen; \
    locale-gen en_US.UTF-8; \
    case "${TARGETARCH}" in \
        amd64) ARCH=x64 ;; \
        arm64) ARCH=aarch64 ;; \
        *) echo "Unsupported architecture: ${TARGETARCH}"; exit 1 ;; \
    esac; \
    curl --retry 3 -Lfso /tmp/graalvm.tar.gz \
        "https://download.oracle.com/graalvm/${JAVA_VERSION}/${GRAAL_VERSION}/graalvm-jdk-${JAVA_VERSION}_linux-${ARCH}_bin.tar.gz"; \
    mkdir -p /opt/java/graalvm; \
    tar -xf /tmp/graalvm.tar.gz -C /opt/java/graalvm --strip-components=1; \
    rm -rf /var/lib/apt/lists/* /tmp/graalvm.tar.gz

ENV JAVA_HOME=/opt/java/graalvm
ENV PATH="${JAVA_HOME}/bin:${PATH}"

# Add Pterodactyl stuff
RUN useradd -d /home/container -m container

USER container

ENV USER=container HOME=/home/container

WORKDIR /home/container

COPY ./entrypoint.sh /entrypoint.sh

CMD ["/bin/bash", "/entrypoint.sh"]
