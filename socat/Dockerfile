ARG SOCAT_VER=1.8.1.1

# ========================================
# Prepare Environment and Tools
# ========================================
FROM alpine:3.23 AS builder

RUN apk add --no-cache build-base linux-headers curl tar perl

ENV OPENSSL_VER=3.6.2 \
    NCURSES_VER=6.6 \
    READLINE_VER=8.3

WORKDIR /build

# =========================================
# Download Dependencies
# =========================================

# Build Ncurses
RUN curl -LO "https://ftp.gnu.org/pub/gnu/ncurses/ncurses-${NCURSES_VER}.tar.gz" && \
    tar xzf ncurses-${NCURSES_VER}.tar.gz && cd ncurses-${NCURSES_VER} && \
    CC='/usr/bin/gcc --static' CFLAGS='-fPIC' ./configure --disable-shared --enable-static && \
    make -j$(nproc) && make install

# Build Readline
RUN curl -LO "https://ftp.gnu.org/pub/gnu/readline/readline-${READLINE_VER}.tar.gz" && \
    tar xzf readline-${READLINE_VER}.tar.gz && cd readline-${READLINE_VER} && \
    CC='/usr/bin/gcc --static' CFLAGS='-fPIC' ./configure --disable-shared --enable-static && \
    make -j$(nproc) && make install

#Build OpenSSL
RUN curl -LO "https://github.com/openssl/openssl/releases/download/openssl-${OPENSSL_VER}/openssl-${OPENSSL_VER}.tar.gz" && \
    tar xzf openssl-${OPENSSL_VER}.tar.gz && cd openssl-${OPENSSL_VER} && \
    CC='/usr/bin/gcc --static' CFLAGS='-fPIC' ./Configure no-shared no-async linux-x86_64 && \
    make -j$(nproc) && make install

# Build Socat
RUN curl -LO "http://www.dest-unreach.org/socat/download/socat-${SOCAT_VER}.tar.gz" && \
    tar xzf socat-${SOCAT_VER}.tar.gz && cd socat-${SOCAT_VER} && \
    CC='/usr/bin/gcc --static' CFLAGS='-fPIC' CPPFLAGS='-DNETDB_INTERNAL=-1' ./configure && \
    make -j$(nproc) && strip socat

# =========================================
# Export static binary Socat
# =========================================
FROM scratch AS exporter
COPY --from=builder /build/socat-${SOCAT_VER}/socat /socat_static