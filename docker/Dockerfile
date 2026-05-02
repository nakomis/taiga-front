# This source code is licensed under the terms of the
# GNU Affero General Public License found in the LICENSE file in
# the root directory of this source tree.
#
# Copyright (c) 2021-present Kaleidos INC

# Build arg is required — no default to prevent accidental use of a stale version.
# Usage: docker build --build-arg TAIGA_NAKOMIS_VERSION=v6.10.0-nakomis.1 .
ARG TAIGA_NAKOMIS_VERSION

FROM nginx:1.23-alpine
LABEL maintainer="support@taiga.io"

ARG TAIGA_NAKOMIS_VERSION

COPY docker/default.conf /etc/nginx/conf.d/default.conf
COPY docker/conf.json.template /
COPY docker/config_env_subst.sh /docker-entrypoint.d/30_config_env_subst.sh

RUN set -eux; \
    test -n "${TAIGA_NAKOMIS_VERSION}" || \
        { echo "ERROR: TAIGA_NAKOMIS_VERSION build arg is required"; \
          echo "  Usage: docker build --build-arg TAIGA_NAKOMIS_VERSION=v6.10.0-nakomis.1 ."; \
          exit 1; }; \
    apk update; \
    apk add \
       bash \
       wget \
       unzip; \
    # Install taiga-front from nakomis fork release
    wget "https://github.com/nakomis/taiga-front/releases/download/${TAIGA_NAKOMIS_VERSION}/taiga-front-${TAIGA_NAKOMIS_VERSION}.zip" -O source.zip; \
    mkdir /dist; \
    unzip source.zip -d /dist; \
    rm source.zip; \
    mv /conf.json.template /dist/; \
    chmod +x /docker-entrypoint.d/30_config_env_subst.sh; \
    # Install taiga-front contribs
    mkdir /dist/plugins; \
    cd /dist/plugins; \
    # Slack
    wget https://github.com/taigaio/taiga-contrib-slack/archive/6.10.0.zip -O source.zip; \
    unzip -j source.zip "taiga-contrib-slack-6.10.0/front/dist/*" -d slack; \
    rm source.zip; \
    # Github
    wget https://github.com/taigaio/taiga-contrib-github-auth/archive/6.10.0.zip -O source.zip; \
    unzip -j source.zip "taiga-contrib-github-auth-6.10.0/front/dist/*" -d github-auth; \
    rm source.zip; \
    # Gitlab
    wget https://github.com/taigaio/taiga-contrib-gitlab-auth/archive/6.10.0.zip -O source.zip; \
    unzip -j source.zip "taiga-contrib-gitlab-auth-6.10.0/front/dist/*" -d gitlab-auth; \
    rm source.zip; \
    cd /; \
    # Ready for nginx
    mv /dist/* /usr/share/nginx/html; \
    rm -rf /dist
