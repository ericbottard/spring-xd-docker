FROM java:7
MAINTAINER Eric Bottard

# 'snapshot' or 'release'
ENV XD_BUILD snapshot
ENV XD_VERSION 1.0.1.BUILD_SNAPSHOT

RUN groupadd -r springxd && useradd -r -g springxd springxd

RUN wget http://repo.spring.io/${XD_BUILD}/org/springframework/xd/spring-xd/${XD_VERSION}/spring-xd-${XD_VERSION}-dist.zip \
    -O /opt/spring-xd-${XD_VERSION}-dist.zip
RUN unzip /opt/spring-xd-${XD_VERSION}-dist.zip -d /opt/

RUN rm /opt/spring-xd-${XD_VERSION}-dist.zip

# Install our ZK
RUN apt-get update && apt-get install -y rsync
RUN /opt/spring-xd-${XD_VERSION}/zookeeper/bin/install-zookeeper

RUN chown -R springxd:springxd /opt/spring-xd-${XD_VERSION}
RUN ln -s /opt/spring-xd-${XD_VERSION} /opt/spring-xd

USER springxd
WORKDIR /opt/spring-xd