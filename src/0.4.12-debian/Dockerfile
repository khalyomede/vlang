FROM debian:13.5-slim AS build

WORKDIR /opt/v

ENV CC="gcc"
ENV VFLAGS="-cc gcc"

RUN apt update && \
    apt upgrade --yes && \
    apt install --yes build-essential git pkg-config libgc-dev && \
    # V
    git clone --no-checkout https://github.com/vlang/v . && \
    git fetch --depth 1 origin abe97b636b16b37eb996bfd45c8b6a28d157b2cf && \
    git checkout FETCH_HEAD && \
    # V compiler
    git clone --filter=blob:none --quiet https://github.com/vlang/vc ./vc && \
    cd vc && \
    VC_COMMIT=$(git log --format='%H %s' | grep -m1 205889b97794697282030e94858c3bc160721e49 | cut -d' ' -f1) && \
    test -n "$VC_COMMIT" && \
    git checkout "$VC_COMMIT"

ARG TARGETARCH

RUN case "$TARGETARCH" in \
    amd64) TCC_BRANCH=thirdparty-linux-amd64 ;; \
    arm64) TCC_BRANCH=thirdparty-linux-aarch64 ;; \
    *) echo "unsupported TARGETARCH: $TARGETARCH" >&2; exit 1 ;; \
    esac && \
    git clone --filter=blob:none --quiet --branch "$TCC_BRANCH" https://github.com/vlang/tccbin ./thirdparty/tcc

RUN cc -std=c99 -w -o v1 ./vc/v.c -lm -lpthread
RUN ./v1 -no-parallel -o v2 cmd/v
RUN ./v2 -o v cmd/v
RUN rm -f v1 v2
RUN /opt/v/v symlink

FROM debian:13.5-slim

COPY --from=build /opt/v /opt/v
COPY --from=build /usr/local/bin /usr/local/bin

ENV CC="gcc"
ENV VFLAGS="-cc gcc"

RUN apt update && \
    apt upgrade --yes && \
    apt install --yes --no-install-recommends \
    build-essential \
    libssl-dev \
    git && \
    apt clean && \
    apt autoremove --yes && \
    useradd -m -d /home/v -s /bin/bash v && \
    chmod 755 /opt/v
