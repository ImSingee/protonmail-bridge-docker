# The build image could be golang, but it currently does not support riscv64. Only debian:bookworm does, at the time of writing.
FROM debian:bookworm AS build

ARG version

# Install dependencies
RUN apt-get update && apt-get install -y golang build-essential libsecret-1-dev

# Build
ADD https://github.com/ProtonMail/proton-bridge.git#${version} /build/
WORKDIR /build/
RUN sed -i 's/127\.0\.0\.1/0.0.0.0/g' proton-bridge/internal/constants/constants.go
RUN make build-nogui vault-editor

FROM debian:bookworm
LABEL maintainer="Bryan <opensource@singee.me>"

# Install dependencies and protonmail bridge
RUN apt-get update \
    && apt-get install -y --no-install-recommends socat pass libsecret-1-0 ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Copy bash scripts
COPY gpgparams entrypoint.sh /protonmail/

# Copy protonmail
COPY --from=build /build/bridge /protonmail/
COPY --from=build /build/proton-bridge /protonmail/
COPY --from=build /build/vault-editor /protonmail/

ENTRYPOINT ["bash", "/protonmail/entrypoint.sh"]
