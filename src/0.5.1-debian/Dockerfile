FROM debian:13.5-slim AS build

WORKDIR /opt/v

RUN apt update
RUN apt upgrade --yes
RUN apt install --yes build-essential git
RUN git clone --no-checkout https://github.com/vlang/v . && git fetch --depth 1 origin c289baab244607cd6fdb7c8b1743079d4fd6a025 && git checkout FETCH_HEAD
RUN make
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
