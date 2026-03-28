FROM golang:1-bookworm AS build

ARG version

# Install build dependencies
# NOTE: Proton Bridge uses CGO and links against libsecret + libfido2 + libcbor + openssl.
# These *-dev packages are required at build time.
RUN apt-get update && apt-get install -y \
    build-essential \
    ca-certificates \
    curl \
    git \
    zip unzip tar \
    pkg-config \
    libsecret-1-dev \
    libfido2-dev \
    libcbor-dev \
    libssl-dev

# Build
ADD https://github.com/ProtonMail/proton-bridge.git#${version} /build/
WORKDIR /build/
RUN sed -i 's/127\.0\.0\.1/0.0.0.0/g' internal/constants/constants.go \
    && sed -i 's#^[[:space:]]*// Check for updates when triggered\.$#    // Disable background update checks in container builds.#' internal/bridge/bridge.go \
    && sed -i 's#^[[:space:]]*bridge\.goUpdate = bridge\.tasks\.PeriodicOrTrigger(.*#    bridge.goUpdate = func() {}#' internal/bridge/bridge.go \
    && sed -i '/bridge\.goUpdate = func() {}/,/defer bridge.goUpdate()/ { /bridge\.goUpdate = func() {}/b; /defer bridge.goUpdate()/d; d; }' internal/bridge/bridge.go \
    && sed -i '/internal\/constants"/d;/internal\/updater"/d' internal/bridge/bridge.go
RUN make build-nogui vault-editor

FROM debian:bookworm
LABEL maintainer="Bryan <opensource@singee.me>"

# Install dependencies and protonmail bridge
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        pass \
        libsecret-1-0 \
        libfido2-1 \
        libcbor0.8 \
        libssl3 \
        ca-certificates \
        tmux \
    && rm -rf /var/lib/apt/lists/*

# Copy bash scripts
COPY gpgparams entrypoint.sh /protonmail/

# Copy protonmail
COPY --from=build /build/bridge /protonmail/
COPY --from=build /build/proton-bridge /protonmail/
COPY --from=build /build/vault-editor /protonmail/

ENTRYPOINT []
CMD ["bash", "/protonmail/entrypoint.sh"]
