FROM ubuntu:xenial
MAINTAINER Piasy Xu (xz4215@gmail.com)

WORKDIR /

ENV ENABLE_SHADOW_SOCKS true
ENV SHADOW_SOCKS_SERVER_ADDR 127.0.0.1
ENV SHADOW_SOCKS_SERVER_PORT 8000
ENV SHADOW_SOCKS_ENC_METHOD encmethod
ENV SHADOW_SOCKS_ENC_PASS encpass

RUN apt-get update -y

# Deps
RUN apt-get install -y vim python-pip polipo wget git gnupg flex bison gperf build-essential zip curl subversion pkg-config libglib2.0-dev libgtk2.0-dev libxtst-dev libxss-dev libpci-dev libdbus-1-dev libgconf2-dev libgnome-keyring-dev libnss3-dev lsb-release sudo locales
RUN pip install shadowsocks

# libsodium
RUN wget https://download.libsodium.org/libsodium/releases/libsodium-1.0.12.tar.gz
RUN tar zxf libsodium-1.0.12.tar.gz
WORKDIR /libsodium-1.0.12
RUN ./configure && make && make install
RUN echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf && ldconfig

WORKDIR /
COPY shadowsocks.json /etc/shadowsocks.json
COPY polipo.config /etc/polipo/config

# install webrtc deps
RUN curl https://chromium.googlesource.com/chromium/src/+/master/build/install-build-deps-android.sh?format=TEXT | base64 -d > install-build-deps-android.sh
RUN curl https://chromium.googlesource.com/chromium/src/+/master/build/install-build-deps.sh?format=TEXT | base64 -d > install-build-deps.sh
RUN chmod +x ./install-build-deps.sh
RUN chmod +x ./install-build-deps-android.sh
RUN echo ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true | debconf-set-selections
RUN ./install-build-deps-android.sh

COPY build.sh /

RUN echo 'source /build.sh' >> /root/.bashrc
RUN echo 'export PATH=$PATH:$DEPOT_TOOLS' >> /root/.bashrc
COPY run.sh /
RUN chmod +x /run.sh

CMD /run.sh
