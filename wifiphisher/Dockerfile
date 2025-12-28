FROM alpine:3.19

RUN apk add --no-cache \
  python3 py3-pip git bash jq \
  iw wireless-tools iproute2 tcpdump net-tools \
  libffi-dev openssl-dev build-base \
  curl tar xz \
  dnsmasq hostapd iptables \
  psmisc procps util-linux \
  libnl3 libnl3-dev

# Install s6-overlay
ENV S6_OVERLAY_VERSION=3.2.1.0

RUN set -eux; \
  ARCH="$(uname -m)"; \
  case "$ARCH" in \
    x86_64) S6ARCH="x86_64" ;; \
    aarch64|arm64) S6ARCH="aarch64" ;; \
    *) echo "Unsupported arch: $ARCH" >&2; exit 1 ;; \
  esac; \
  curl -fsSL -o /tmp/s6-noarch.tar.xz \
    "https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-noarch.tar.xz"; \
  curl -fsSL -o /tmp/s6-arch.tar.xz \
    "https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-${S6ARCH}.tar.xz"; \
  tar -C / -Jxpf /tmp/s6-noarch.tar.xz; \
  tar -C / -Jxpf /tmp/s6-arch.tar.xz; \
  rm -f /tmp/s6-noarch.tar.xz /tmp/s6-arch.tar.xz; \
  test -x /init

# Clone + install wifiphisher (IMPORTANT: cd into repo before pip install)
WORKDIR /opt
RUN git clone https://github.com/wifiphisher/wifiphisher.git /opt/wifiphisher \
  && cd /opt/wifiphisher \
  && pip3 install --break-system-packages .

# Copy s6 services to the right place
COPY rootfs/ /

# Ensure service scripts are executable
RUN chmod +x /etc/services.d/wifiphisher/run \
    && chmod +x /etc/services.d/wifiphisher/finish

CMD ["/init"]
