ARG BUILD_FROM
FROM $BUILD_FROM AS build

# Add env
ENV LANG C.UTF-8

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]


# Build dependencies
RUN apk add --no-cache \
    rust cargo build-base git \
    protobuf-dev alsa-lib-dev openssl-dev dbus-dev

# Build librespot
RUN git clone --depth=1 https://github.com/librespot-org/librespot.git /src && \
    cd /src && \
    cargo build --release --no-default-features --features "native-tls alsa-backend with-libmdns"



########################

FROM $BUILD_FROM as prod

RUN \
    apk add --no-cache \
        alsa-lib alsa-utils bash \
    && rm -fr \
        /tmp/*

COPY --from=build /src/target/release/librespot /usr/bin/librespot

RUN apk add --no-cache --repository=http://dl-cdn.alpinelinux.org/alpine/edge/community/ snapcast-server=0.34.0-r0

RUN \
    wget https://github.com/badaix/snapweb/releases/download/v0.9.2/snapweb.zip && \
    unzip -o snapweb.zip -d /usr/share/snapserver/snapweb/ && \
    rm snapweb.zip

# Copy data for add-on
COPY run.sh /
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
