FROM marketplace.gcr.io/google/debian9
ENV GRAFANA_VERSION 5.4.5
ENV GRAFANA_SHA256 89c4f576114179617060c977afcf69a71228d9f389bbc89c4913754133c4736f

ENV C2D_RELEASE 5.4.5

ARG GRAFANA_URL="https://dl.grafana.com/oss/release/grafana-$GRAFANA_VERSION.linux-amd64.tar.gz"
ARG GF_UID="472"
ARG GF_GID="472"

ENV PATH=/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin \
    GF_PATHS_CONFIG="/etc/grafana/grafana.ini" \
    GF_PATHS_DATA="/var/lib/grafana" \
    GF_PATHS_HOME="/usr/share/grafana" \
    GF_PATHS_LOGS="/var/log/grafana" \
    GF_PATHS_PLUGINS="/var/lib/grafana/plugins" \
    GF_PATHS_PROVISIONING="/etc/grafana/provisioning"

RUN set -x && \
    buildDependencies=' \
      ca-certificates \
      libfontconfig \
      tar \
      wget \
    ' && \
    apt-get update && apt-get install -qq -y $buildDependencies && \
    mkdir -p "$GF_PATHS_HOME/.aws" && \
    wget -O grafana.tar.gz "$GRAFANA_URL" && \
    echo "$GRAFANA_SHA256 grafana.tar.gz" | sha256sum -c - && \
    tar xfvz grafana.tar.gz --strip-components=1 -C "$GF_PATHS_HOME" && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* && \
    groupadd -r -g $GF_GID grafana && \
    useradd -r -u $GF_UID -g grafana grafana && \
    mkdir -p "$GF_PATHS_PROVISIONING/datasources" \
             "$GF_PATHS_PROVISIONING/dashboards" \
             "$GF_PATHS_LOGS" \
             "$GF_PATHS_PLUGINS" \
             "$GF_PATHS_DATA" && \
    cp "$GF_PATHS_HOME/conf/sample.ini" "$GF_PATHS_CONFIG" && \
    cp "$GF_PATHS_HOME/conf/ldap.toml" /etc/grafana/ldap.toml && \
    chown -R grafana:grafana "$GF_PATHS_DATA" "$GF_PATHS_HOME/.aws" "$GF_PATHS_LOGS" "$GF_PATHS_PLUGINS" && \
    chmod 777 "$GF_PATHS_DATA" "$GF_PATHS_HOME/.aws" "$GF_PATHS_LOGS" "$GF_PATHS_PLUGINS"

EXPOSE 3000

COPY ./docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod 777 /docker-entrypoint.sh

USER grafana
WORKDIR /
ENTRYPOINT [ "/docker-entrypoint.sh" ]
