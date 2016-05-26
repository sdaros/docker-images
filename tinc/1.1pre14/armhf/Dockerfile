FROM resin/rpi-raspbian

ENV TINC_FILENAME tinc-1.1pre14.tar.gz
ENV TINC_VERSION 1.1pre14
ENV TINC_GPG_KEY D62BDD168EFBE48BC60E8E234A6084B9C0D71F4A

# Install tinc dependencies
RUN apt-get update \
  && DEBIAN_FRONTEND=noninteractive \
  apt-get -y -q install libncurses-dev \
  build-essential \
  libreadline-dev \
  libssl-dev \
  liblzo2-dev \
  zlib1g-dev \
  curl \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/

# Download tinc and verify GPG signature
RUN curl -fLSO "https://tinc-vpn.org/packages/$TINC_FILENAME" \
  && curl -fLSO "https://tinc-vpn.org/packages/$TINC_FILENAME.sig" \
  && export GNUPGHOME="$(mktemp -d)" \
  && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$TINC_GPG_KEY" \
  && gpg --verify "$TINC_FILENAME.sig" \
  && rm -r "$GNUPGHOME" "$TINC_FILENAME.sig"

# Configure and compile binary
RUN mkdir -p /usr/local/src \
  && tar -xf "$TINC_FILENAME" -C /usr/local/src \
  && mv "/usr/local/src/tinc-$TINC_VERSION" /usr/local/src/tinc \
  && rm "$TINC_FILENAME" \
  && cd /usr/local/src/tinc \
  && ./configure \
  && make -j"$(getconf _NPROCESSORS_ONLN)" \
  && make install \
  && make clean

ENTRYPOINT [ "tincd", "-D", "-c", "/etc/tinc/heimdall", "-U", "nobody" ]
