ARG BUILD_FROM
FROM $BUILD_FROM as builder
MAINTAINER dscao.com@gmail.com

ENV LANG C.UTF-8

# Copied with minor edits from https://github.com/hertzg/rtl_433_docker/blob/master/images/alpine/build-context/Dockerfile
RUN apk add --no-cache --virtual .buildDeps \
    build-base \
    libusb-dev \
    librtlsdr-dev \
    cmake \
    git

WORKDIR /build
RUN git clone https://github.com/dscao/rtl_433_sonoff
WORKDIR ./rtl_433_sonoff

ARG rtl433GitRevision=719143abb53b5b0b19de86a3985da8beffacd7dc
RUN git checkout ${rtl433GitRevision}
WORKDIR ./build
RUN cmake ..
RUN make -j 4

WORKDIR /build/root
WORKDIR /build/rtl_433_sonoff/build
RUN make DESTDIR=/build/root/ install
RUN ls -lah /build/root

FROM $BUILD_FROM

ENV LANG C.UTF-8

RUN apk add --no-cache libusb \
    librtlsdr
WORKDIR /root
COPY --from=builder /build/root/ /

# Run script
COPY run.sh /
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
