FROM ubuntu:xenial

LABEL maintainer="HugeGraph Docker Maintainers <hugegraph@googlegroups.com>"

ENV PKG_URL https://github.com/hugegraph

RUN  sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
RUN  apt-get clean

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

# 2. Init HugeGraph Sever
# (Optional) You can set the ip of github to speed up the local build
# && echo "192.30.253.112 github.com\n151.101.44.249 github.global.ssl.fastly.net" >> /etc/hosts \
ENV SERVER_VERSION 0.10.4
RUN set -e \
    && mkdir -p /root/hugegraph-server \
    && curl -L -S ${PKG_URL}/hugegraph/releases/download/v${SERVER_VERSION}/hugegraph-${SERVER_VERSION}.tar.gz -o /root/server.tar.gz \
    && tar xzf /root/server.tar.gz --strip-components 1 -C /root/hugegraph-server \
    && rm /root/server.tar.gz \
    && cd /root/hugegraph-server/ \
    && sed -i "s/^restserver.url.*$/restserver.url=http:\/\/0.0.0.0:8080/g" ./conf/rest-server.properties \
    && sed -n '63p' ./bin/start-hugegraph.sh | grep "&" > /dev/null && sed -i 63{s/\&$/#/g} ./bin/start-hugegraph.sh \
    && sed -n '74p' ./bin/start-hugegraph.sh | grep "exit" > /dev/null && sed -i 74{s/^/#/g} ./bin/start-hugegraph.sh \
    && ./bin/init-store.sh

EXPOSE 8080
WORKDIR /root/hugegraph-server
VOLUME /root/hugegraph-server/rocksdb-data

ENTRYPOINT ["./bin/start-hugegraph.sh"]