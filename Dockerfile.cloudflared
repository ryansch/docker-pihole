FROM ubuntu:jammy
LABEL maintainer="Ryan Schlesinger <ryan@ryanschlesinger.com>"

ARG CLOUDFLARED_VERSION=2022.7.1

SHELL ["/bin/bash", "-o", "pipefail", "-c"]
ENV DEBIAN_FRONTEND=noninteractive

RUN set -eux; \
      \
      apt-get update -y; \
      apt-get install -y \
        ca-certificates \
        wget \
      ; \
      apt-get clean; \
      rm -f /var/lib/apt/lists/*_*

ENV CLOUDFLARED_VERSION=${CLOUDFLARED_VERSION}
RUN set -eux; \
      \
      wget -O /tmp/cloudflared-linux-arm64 https://github.com/cloudflare/cloudflared/releases/download/${CLOUDFLARED_VERSION}/cloudflared-linux-arm64; \
      mv /tmp/cloudflared-linux-arm64 /usr/local/bin/cloudflared; \
      chmod +x /usr/local/bin/cloudflared; \
      cloudflared -v

ENTRYPOINT ["cloudflared", "--no-autoupdate"]
CMD ["version"]
