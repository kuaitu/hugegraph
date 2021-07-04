FROM ubuntu:xenial

LABEL maintainer="HugeGraph Docker Maintainers <hugegraph@googlegroups.com>"

ENV PKG_URL https://github.com/hugegraph
ENV HUGEGRAPH_SERVER hugegraph-server

# 1. Install needed dependencies of GraphServer & RocksDB
RUN set -x \
    && apt-get -q update \
    && apt-get -q install -y --no-install-recommends --no-install-suggests \
       curl \
       lsof \
       g++ \
       gcc \
       openjdk-8-jdk \
    && apt-get clean
    # && rm -rf /var/lib/apt/lists/*

# 3. Prepare for HugeGraph Studio
ENV STUDIO_VERSION 0.10.0
# (Optional) You can set the ip of github to speed up the local build
# && echo "192.30.253.112 github.com\n151.101.44.249 github.global.ssl.fastly.net" >> /etc/hosts \
RUN set -e \
    && mkdir -p /root/hugegraph-studio \
    && curl -L -S ${PKG_URL}/hugegraph-studio/releases/download/v${STUDIO_VERSION}/hugegraph-studio-${STUDIO_VERSION}.tar.gz -o /root/studio.tar.gz \
    && tar xzf /root/studio.tar.gz --strip-components 1 -C /root/hugegraph-studio \
    && rm /root/studio.tar.gz \
    && cd /root/hugegraph-studio/ \
    && sed -i "s/^studio.server.host.*$/studio.server.host=0.0.0.0/g" ./conf/hugegraph-studio.properties \
    && sed -i "s/^graph.server.host.*$/graph.server.host=${HUGEGRAPH_SERVER}/g" ./conf/hugegraph-studio.properties

EXPOSE 8088
WORKDIR /root/hugegraph-studio
#VOLUME /root

ENTRYPOINT ["./bin/hugegraph-studio.sh"]