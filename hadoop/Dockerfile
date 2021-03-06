#
#  Author: Hari Sekhon
#  Date: 2016-04-24 21:18:57 +0100 (Sun, 24 Apr 2016)
#
#  vim:ts=4:sts=4:sw=4:et
#
#  https://github.com/harisekhon/Dockerfiles
#
#  If you're using my code you're welcome to connect with me on LinkedIn and optionally send me feedback
#
#  https://www.linkedin.com/in/harisekhon
#

FROM harisekhon/centos-java:latest
MAINTAINER Hari Sekhon (https://www.linkedin.com/in/harisekhon)

#ARG HADOOP_VERSION=1.2.1
#ARG HADOOP_VERSION=2.5.2
#ARG HADOOP_VERSION=2.6.4
ARG HADOOP_VERSION=2.7.3

ARG TAR=hadoop-$HADOOP_VERSION.tar.gz

ENV PATH $PATH:/hadoop/bin

LABEL Description="Hadoop Dev", \
      "Hadoop Version"="$HADOOP_VERSION"

WORKDIR /

RUN yum install -y openssh-server openssh-clients tar which

RUN yum install -y wget hostname && \
    # --max-redirect - some apache mirrors redirect a couple times and give you the latest version instead
    #                  but this breaks stuff later because the link will not point to the right dir
    #                  (and is also the wrong version for the tag)
    wget -t 10 --max-redirect 1 --retry-connrefused -O "$TAR" "http://www.apache.org/dyn/closer.lua?filename=hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-$HADOOP_VERSION.tar.gz&action=download" || \
    wget -t 10 --max-redirect 1 --retry-connrefused -O "$TAR" "http://archive.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-$HADOOP_VERSION.tar.gz" && \
    tar zxf "$TAR" && \
    # check tarball was extracted to the right place, helps ensure it's the right version and the link will work
    test -d "hadoop-$HADOOP_VERSION" && \
    ln -sv "hadoop-$HADOOP_VERSION" hadoop && \
    rm -fv "$TAR" && \
    { rm -rf hadoop/share/doc; : ; } && \
    # Hadoop 1.x
    #/hadoop/bin/hadoop namenode -format && \
    # Hadoop 2.x
    /hadoop/bin/hdfs namenode -format && \
    # removing tar tries to remove systemd, which breaks
    yum remove -y wget && \
    yum autoremove -y && \
    # gets autoremoved, ensure it's added back as Hadoop scripts need it
    yum install -y hostname && \
    yum clean all

COPY entrypoint.sh /
COPY conf/core-site.xml /hadoop/etc/hadoop/
COPY conf/hdfs-site.xml /hadoop/etc/hadoop/
COPY conf/yarn-site.xml /hadoop/etc/hadoop/
COPY conf/mapred-site.xml /hadoop/etc/hadoop/
COPY profile.d/hadoop.sh /etc/profile.d/

EXPOSE 8020 8042 8088 9000 10020 19888 50010 50020 50070 50075 50090

CMD "/entrypoint.sh"
