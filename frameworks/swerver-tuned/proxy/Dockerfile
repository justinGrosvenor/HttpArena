# swerver-as-gateway edge: a pure swerver reverse proxy terminating TLS + h2 + h3
# on 8443 and forwarding every endpoint to the swerver-tuned backend over h1.
# "tuned" here is the backend (precompressed static cache, async PG); the edge
# is stock swerver acting as its own API gateway (no Caddy) — swerver ships a
# full reverse proxy, so the gateway profile exercises swerver end to end.
FROM debian:trixie AS build

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates curl xz-utils git libssl-dev zlib1g-dev dpkg-dev pkg-config python3 \
    && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    DPKG_ARCH=$(dpkg --print-architecture); \
    case "${DPKG_ARCH}" in \
        amd64) ZIG_ARCH=x86_64 ;; \
        arm64) ZIG_ARCH=aarch64 ;; \
        *) echo "unsupported arch: ${DPKG_ARCH}" >&2; exit 1 ;; \
    esac; \
    curl -fsSL "https://ziglang.org/download/0.16.0/zig-${ZIG_ARCH}-linux-0.16.0.tar.xz" \
        -o /tmp/zig.tar.xz; \
    mkdir -p /opt/zig; \
    tar -xJf /tmp/zig.tar.xz -C /opt/zig --strip-components=1; \
    rm /tmp/zig.tar.xz; \
    ln -s /opt/zig/zig /usr/local/bin/zig; \
    zig version

WORKDIR /src
RUN git clone --depth 1 --branch v0.1.0-alpha.21 \
        https://github.com/justinGrosvenor/swerver.git .

RUN MULTIARCH=$(dpkg-architecture -qDEB_HOST_MULTIARCH); \
    for lib in libssl.so libssl.a libcrypto.so libcrypto.a; do \
        ln -sf "/usr/lib/${MULTIARCH}/${lib}" "/usr/lib/${lib}"; \
    done

# Pure swerver binary with TLS + h2 + h3 + reverse-proxy support. ReleaseFast
# is required, not just for speed: the default Debug build overflows the forked
# worker's stack zeroing the HTTP/2 stack struct (http2.zig init).
RUN zig build -Doptimize=ReleaseFast -Denable-tls=true -Denable-http2=true -Denable-http3=true -Denable-proxy=true --summary all

# ── runtime stage ──
FROM debian:trixie-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates libssl3 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build /src/zig-out/bin/swerver /usr/local/bin/swerver
COPY config-proxy.json /etc/swerver/config-proxy.json

EXPOSE 8443/tcp 8443/udp

CMD ["/usr/local/bin/swerver", "--config", "/etc/swerver/config-proxy.json"]
