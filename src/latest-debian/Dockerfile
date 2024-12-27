FROM debian:9-slim AS build

WORKDIR /opt/v

RUN apt update
RUN apt upgrade --yes
RUN apt install --yes build-essential git
RUN git clone --depth 1 --single-branch --branch master https://github.com/vlang/v .
RUN make
RUN /opt/v/v symlink

FROM debian:9-slim

COPY --from=build /opt/v /opt/v
COPY --from=build /usr/local/bin /usr/local/bin

ENV CC="gcc"
ENV VFLAGS="-cc gcc"

RUN apt update && apt upgrade --yes && apt install --yes build-essential libssl-dev git && apt clean && apt autoremove --yes
