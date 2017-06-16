#
#  Author: Hari Sekhon
#  Date: 2016-01-16 09:58:07 +0000 (Sat, 16 Jan 2016)
#
#  vim:ts=4:sts=4:sw=4:et
#
#  https://github.com/harisekhon/Dockerfiles/nagios-plugins
#
#  If you're using my code you're welcome to connect with me on LinkedIn and optionally send me feedback to help improve or steer this or other code I publish
#
#  https://www.linkedin.com/in/harisekhon
#

FROM ubuntu:latest
MAINTAINER Hari Sekhon (https://www.linkedin.com/in/harisekhon)

LABEL Description="Advanced Nagios Plugins Collection (on Ubuntu)"

ENV PATH $PATH:/github/nagios-plugins

RUN mkdir -v /github

WORKDIR /github

# drops in to /bin/sh pushd not available, could bash -c but explicit paths are good enough
RUN \
    x=nagios-plugins; \
    apt-get update && \
    apt-get install -y git make && \
    git clone https://github.com/harisekhon/$x /github/$x && \
    cd /github/$x && \
    make build zookeeper clean && \
    make apt-packages-remove && \
    tests/help.sh || \
    { for x in /home/travis/.cpanm/work/*/build.log; do echo "uploading $x"; curl -sT "$x" transfer.sh || :; curl -sT "$x" chunk.io || :; done; exit 1; }

WORKDIR /github/nagios-plugins

# trying to do -exec basename {} \; results in only the jython files being printed
CMD /bin/bash -c "find /github/nagios-plugins -maxdepth 2 -type f -iname '[A-Za-z]*.pl' -o -iname '[A-Za-z]*.py' | grep -iv makefile | xargs -n1 basename | sort"