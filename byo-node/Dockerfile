# Specifing the docker file to build own node image based on the debian slim version
FROM debian:bookworm-slim

RUN apt-get update \
    && apt-get install -y curl \
    && curl -fsSL https://deb.nodesource.com/setup_<node_version>.x | bash - \
    && apt-get install -y nodejs \
    && sudo -E bash nodesource_setup.sh \
    && apt-get install -y nodejs \
    && node --version \
    && npm --version 

