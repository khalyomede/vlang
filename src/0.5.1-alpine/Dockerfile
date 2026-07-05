FROM alpine:3.23.5 AS build

WORKDIR /opt/v

RUN apk add --no-cache make git musl-dev gcc
RUN git clone --no-checkout https://github.com/vlang/v . && git fetch --depth 1 origin c289baab244607cd6fdb7c8b1743079d4fd6a025 && git checkout FETCH_HEAD
RUN make
RUN /opt/v/v symlink

FROM alpine:3.23.5

COPY --from=build /opt/v /opt/v
COPY --from=build /usr/local/bin /usr/local/bin

ENV CC="gcc"
ENV VFLAGS="-cc gcc"

RUN apk add --no-cache \
    gcc \
    gc-dev \
    musl-dev \
    openssl-dev \
    git && \
    adduser -D -h /home/v -s /bin/sh v && \
    chmod 755 /opt/v && \
    mkdir -p /home/v && \
    chown v:v /home/v
