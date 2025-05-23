# syntax=docker/dockerfile:1
FROM alpine:3.21
ARG TARGETPLATFORM
ARG TARGETOS
ARG TARGETARCH
ARG TARGETVARIANT
ARG BUILDPLATFORM
ARG BUILDOS
ARG BUILDARCH
ARG BUILDVARIANT
RUN set -eu; \
    echo "TARGETPLATFORM=$TARGETPLATFORM"; \
    echo "TARGETOS=$TARGETOS"; \
    echo "TARGETARCH=$TARGETARCH"; \
    echo "TARGETVARIANT=$TARGETVARIANT"; \
    echo "BUILDPLATFORM=$BUILDPLATFORM"; \
    echo "BUILDOS=$BUILDOS"; \
    echo "BUILDARCH=$BUILDARCH"; \
    echo "BUILDVARIANT=$BUILDVARIANT";

# Install isync
RUN apk add --no-cache ca-certificates
RUN set -eux; \
    apk add --no-cache isync~1.5.0; \
    # For mbsync-get-cert to get a self-signed certificate
    apk add --no-cache openssl; \
    mbsync --version

# Install pingme
RUN set -eux; \
    PINGME_VERSION=v0.2.6; \
    case "$( uname -m )" in \
        'x86') \
            URL="https://github.com/kha7iq/pingme/releases/download/v0.2.6/pingme_Linux_x86_64.tar.gz"; \
            SHA256=747e05242f4d1448f2bb7d5cc8903683ec75fa94fa95f2ec1704b0a880a7b9e9; \
            ;; \
        'x86_64') \
            URL="https://github.com/kha7iq/pingme/releases/download/v0.2.6/pingme_Linux_x86_64.tar.gz"; \
            SHA256=2f224389804b433318adaa5b5a4debe9974d8f17a03d2909e4c651cb723a4bed; \
            ;; \
        'armv7l') \
            URL="https://github.com/kha7iq/pingme/releases/download/v0.2.6/pingme_Linux_arm.tar.gz"; \
            SHA256=7feeac9c9a0c353da2a03f0e3eb67aab6bc4a31248c0f3c694a80f15156804d9; \
            ;; \
        'aarch64') \
            URL="https://github.com/kha7iq/pingme/releases/download/v0.2.6/pingme_Linux_arm64.tar.gz"; \
            SHA256=06ead384717a718366d032c11b223ec5285705623c9507a8e3432bc7e4cd6e89; \
            ;; \
        *) \
            echo "Architecture not supported"; \
            exit 1; \
            ;; \
    esac; \
    FILE=pingme.tar.gz; \
    wget -q "$URL" -O "$FILE"; \
    echo "$SHA256  $FILE" | sha256sum -c -; \
    mkdir -p extract; \
    tar -C extract -xvf "$FILE" --no-same-owner --no-same-permissions -- pingme LICENSE.md; \
    mkdir -pv /usr/local/bin; \
    BIN=$( find extract -type f -name "pingme" | head -n1 ); \
    mv -v "$BIN" /usr/local/bin/pingme; \
    chmod +x /usr/local/bin/pingme; \
    pingme --version; \
    mkdir -p /licenses; \
    mv -v extract/LICENSE.md /licenses/LICENSE.md; \
    rm -rf extract; \
    rm -f "$FILE"; \
    :

# Install notification tools
RUN apk add --no-cache curl jq

# Install copy tools
RUN apk add --no-cache rsync

# Install helper scripts
COPY sync /sync
RUN chmod +x /sync

WORKDIR /mail
VOLUME /mail

COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT [ "/docker-entrypoint.sh" ]
CMD [ "/sync" ]
