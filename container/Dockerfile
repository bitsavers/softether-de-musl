FROM alpine:3.21 AS builder

ARG SOFTETHER_GIT_TAG="5.02.5187"

ENV LANG="en_US.UTF-8" \
    USE_MUSL=YES

# https://github.com/SoftEtherVPN/SoftetherVPN-docker/blob/master/Dockerfile
# https://github.com/SoftEtherVPN/SoftEtherVPN/blob/master/src/BUILD_UNIX.md
RUN set -ex; \
    apk add --no-cache --update \
    binutils \
    linux-headers \
    build-base \
    readline-dev \
    openssl-dev \
    ncurses-dev \
    git \
    cmake \
    zlib-dev \
    libsodium-dev \
    gnu-libiconv; \
    mkdir -p /app/softether-vpn; \
    git clone --depth 1 --branch ${SOFTETHER_GIT_TAG} https://github.com/SoftEtherVPN/SoftEtherVPN.git /app/softether-vpn; \
    cd /app/softether-vpn; \
    git submodule init && git submodule update; \
    ./configure; \
	make -j $(nproc) -C build

FROM scratch AS dist
COPY --from=builder /app/softether-vpn/build/libcedar.so /app/softether-vpn/build/libmayaqua.so /usr/local/lib/
COPY --from=builder /app/softether-vpn/build/vpncmd /app/softether-vpn/build/hamcore.se2 /usr/vpncmd/
COPY --from=builder /app/softether-vpn/build/vpnserver /usr/vpnserver/
COPY --from=builder /app/softether-vpn/build/vpnclient /usr/vpnclient/
COPY --from=builder /app/softether-vpn/build/vpnbridge /usr/vpnbridge/
