FROM alpine

LABEL author=yondwell<yondwell@gmail.com>
CMD ["/bin/sh"]

ARG NACOS_VERSION=1.3.2
ARG MYSQL_VERSION=8.0.20

# set environment
ENV MODE="cluster" \
    PREFER_HOST_MODE="ip"\
    BASE_DIR="/home/nacos" \
    CLASSPATH=".:/home/nacos/conf:$CLASSPATH" \
    CLUSTER_CONF="/home/nacos/conf/cluster.conf" \
    FUNCTION_MODE="all" \
    JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk" \
    NACOS_USER="nacos" \
    JAVA="/usr/lib/jvm/java-1.8.0-openjdk/bin/java" \
    JVM_XMS="2g" \
    JVM_XMX="2g" \
    JVM_XMN="1g" \
    JVM_MS="128m" \
    JVM_MMS="320m" \
    NACOS_DEBUG="n" \
    TOMCAT_ACCESSLOG_ENABLED="false" \
    TIME_ZONE="Asia/Shanghai"


WORKDIR /$BASE_DIR

RUN set -x \
    && apk update \
    && apk add openjdk8 wget iputils libcurl \
    && wget  https://github.com/alibaba/nacos/releases/download/${NACOS_VERSION}/nacos-server-${NACOS_VERSION}.tar.gz -P /home \
    && tar -xzvf /home/nacos-server-${NACOS_VERSION}.tar.gz -C /home \
    && mkdir -p /home/nacos/plugins/mysql/ \
    && wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-${MYSQL_VERSION}.tar.gz -P /home/tmp \
    && tar -xzvf /home/tmp/mysql-connector-java-${MYSQL_VERSION}.tar.gz -C /home/tmp \
    && mv /home/tmp/mysql-connector-java-${MYSQL_VERSION}/mysql-connector-java-${MYSQL_VERSION}.jar /home/nacos/plugins/mysql/mysql-connector-java-${MYSQL_VERSION}.jar \
    && rm -rf /home/nacos-server-${NACOS_VERSION}.tar.gz /home/nacos/bin/* /home/nacos/conf/*.properties /home/nacos/conf/*.example /home/nacos/conf/nacos-mysql.sql /home/tmp \
    && ln -snf /usr/share/zoneinfo/$TIME_ZONE /etc/localtime \
    && echo '$TIME_ZONE' > /etc/timezone \
    && mkdir -p logs \
    && cd logs \
    && touch start.ou \
    && ln -sf /dev/stdout start.out \
    && ln -sf /dev/stderr start.out




ADD bin/docker-startup.sh bin/docker-startup.sh
ADD conf/application.properties conf/application.properties
ADD init.d/custom.properties init.d/custom.properties


RUN chmod +x bin/docker-startup.sh

EXPOSE 8848
ENTRYPOINT ["bin/docker-startup.sh"]