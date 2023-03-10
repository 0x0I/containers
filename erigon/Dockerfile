ARG VERSION=2.37.0

# ******* Stage: setup ******* #
FROM golang:1.19.5-buster AS setup

ARG VERSION

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends git make apt-transport-https ca-certificates

WORKDIR /

# Installing Erigon
RUN git clone -b "v${VERSION}" --recurse-submodules -j8 --progress https://github.com/ledgerwatch/erigon && cd erigon && make erigon

# ******* Stage: release ******* #
FROM debian:stable

RUN apt-get update && apt-get install --yes --no-install-recommends tini apt-transport-https ca-certificates

COPY --from=setup /erigon/build/bin/erigon /usr/local/bin
COPY ./entrypoints/entrypoint.sh /entrypoint.sh

# default ports
#
#       rpc    p2p   auth  metrics
#        ↓      ↓     ↓     ↓
EXPOSE  8545  30303  8551  6060

ENTRYPOINT ["/entrypoint.sh"]
CMD ["erigon"]
