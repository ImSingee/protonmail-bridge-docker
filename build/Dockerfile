FROM golang:1-bookworm AS build

ARG version

# Install dependencies
RUN apt-get update && apt-get install -y build-essential libsecret-1-dev

# Build
ADD https://github.com/ProtonMail/proton-bridge.git#${version} /build/
WORKDIR /build/
RUN sed -i 's/127\.0\.0\.1/0.0.0.0/g' internal/constants/constants.go
RUN make build-nogui vault-editor

FROM debian:bookworm
LABEL maintainer="Bryan <opensource@singee.me>"

# Install dependencies and protonmail bridge
RUN apt-get update \
    && apt-get install -y --no-install-recommends pass libsecret-1-0 ca-certificates tmux \
    && rm -rf /var/lib/apt/lists/*

# Copy bash scripts
COPY gpgparams entrypoint.sh /protonmail/

# Copy protonmail
COPY --from=build /build/bridge /protonmail/
COPY --from=build /build/proton-bridge /protonmail/
COPY --from=build /build/vault-editor /protonmail/

ENTRYPOINT []
CMD ["bash", "/protonmail/entrypoint.sh"]
