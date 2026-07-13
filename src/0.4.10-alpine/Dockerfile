FROM alpine:3.23.5 AS build

WORKDIR /opt/v

RUN apk add --no-cache make git musl-dev gcc gc-dev
RUN git clone --no-checkout https://github.com/vlang/v . && \
    git fetch --depth 1 origin 9387fe73e3407a4aa630e3bbe54c87a5bf2def1e && \
    git checkout FETCH_HEAD && \
    # V compiler
    git clone --filter=blob:none --quiet https://github.com/vlang/vc ./vc && \
    cd vc && \
    VC_COMMIT=$(git log --format='%H %s' | grep -m1 fd78da5dd9e04615920def438335b0a9bea9879a | cut -d' ' -f1) && \
    test -n "$VC_COMMIT" && \
    git checkout "$VC_COMMIT"

ARG TARGETARCH

RUN case "$TARGETARCH" in \
    amd64) TCC_BRANCH=thirdparty-linuxmusl-amd64 ;; \
    arm64) TCC_BRANCH=thirdparty-linuxmusl-aarch64 ;; \
    *) echo "unsupported TARGETARCH: $TARGETARCH" >&2; exit 1 ;; \
    esac && \
    git clone --filter=blob:none --quiet --branch "$TCC_BRANCH" https://github.com/vlang/tccbin ./thirdparty/tcc

RUN cc -std=c99 -w -o v1 ./vc/v.c -lm -lpthread
RUN ./v1 -no-parallel -o v2 cmd/v
RUN ./v2 -o v cmd/v
RUN rm -f v1 v2
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
