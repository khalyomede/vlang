FROM ubuntu:20.04 AS build

WORKDIR /opt/v

RUN apt update
RUN apt upgrade --yes
RUN apt install --yes build-essential git
RUN git clone --depth 1 --single-branch --branch master https://github.com/vlang/v .
RUN make
RUN /opt/v/v symlink

FROM ubuntu:20.04

COPY --from=build /opt/v /opt/v
COPY --from=build /usr/local/bin /usr/local/bin

ENV CC="gcc"
ENV VFLAGS="-cc gcc"

RUN apt update && apt upgrade --yes && apt install --yes build-essential libssl-dev git && apt clean && apt autoremove
