# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM=ghcr.io/hassio-addons/base:16.3.4
FROM $BUILD_FROM

# See https://github.com/librespot-org/librespot/wiki/Compiling#addition-features for librespot compile features.
RUN \
	apk add --no-cache --virtual .build-dependencies \
        build-base \
        cargo \
        git \
        protobuf-dev \
        pulseaudio-dev \
        avahi-dev \
        autoconf \
        automake \
        libtool \
        pkgconf \  
        gcc \
        g++ \
        make \
        cmake
        #rust-bindgen
RUN \
	apk add --no-cache \
        dbus \
        nss \
        avahi-compat-libdns_sd \
        avahi \
        pulseaudio \
        alsa-plugins-pulse \
	    snapcast \
        alsa-utils \
        alsa-utils-doc \
        alsa-lib \
        alsaconf \
        alsa-ucm-conf \
        libupnp-dev \
        libnice-gstreamer \
        gst-plugins-base \
        gst-plugins-ugly \
        gst-plugins-good \
        gst-plugins-bad \
        gst-plugins-good-gtk \
        gst-libav \
        gstreamer-dev \
        gstreamer-tools

        # RUN \
# 	cargo install \
#         --locked \
#         --no-default-features \
#         --features with-dns-sd \
#         --root /usr \
#         --bin librespot \
#         --version 0.6.0 \
#         --verbose \
#         -- librespot

RUN apk add --no-cache librespot --repository=https://dl-cdn.alpinelinux.org/alpine/edge/testing
RUN apk add --no-cache snapweb --repository=https://dl-cdn.alpinelinux.org/alpine/edge/testing

RUN \
        addgroup root audio
RUN \
	git clone https://github.com/hzeller/gmrender-resurrect.git \
	&& mkdir -p /gmrender-resurrect \
	&& cd /gmrender-resurrect \
	&& ./autogen.sh \
	&& ./configure \
	&& automake \
	&& make \
	&& make install
RUN \
	apk del --no-cache --purge .build-dependencies \
	&& rm -fr \
		/tmp/* \
		~/.cargo \
	        /usr/.crates.toml \
	        /usr/.crates2.json \
                /gmrender-resurrect
RUN \
        mkdir -p /var/run/dbus
        # && touch /var/run/dbus/system_bus_socket

# https://stackoverflow.com/a/75295879 dbus daemon slow start fix
RUN sed -i 's/ systemd//g' /etc/nsswitch.conf

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="DjFabFab" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="DjFabFab's addons" \
    org.opencontainers.image.authors="DjFabFab" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
