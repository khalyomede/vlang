FROM alpine:3.21.0 AS build

WORKDIR /opt/v

RUN apk add --no-cache make git musl-dev gcc
RUN git clone --depth 1 --single-branch --branch 0.4.11 https://github.com/vlang/v .
RUN make
RUN /opt/v/v symlink

FROM alpine:3.21.0

COPY --from=build /opt/v /opt/v
COPY --from=build /usr/local/bin /usr/local/bin

ENV CC="gcc"
ENV VFLAGS="-cc gcc"

RUN apk add --no-cache gcc musl-dev openssl-dev git
