# vim:set ft=dockerfile:
FROM debian:jessie

# Source Dockerfile:
# https://github.com/docker-library/postgres/blob/master/9.4/Dockerfile

# explicitly set user/group IDs
RUN groupadd -r freeswitch --gid=999 && useradd -r -g freeswitch --uid=999 freeswitch

# grab gosu for easy step-down from root
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates wget && rm -rf /var/lib/apt/lists/* \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.2/gosu-$(dpkg --print-architecture)" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/1.2/gosu-$(dpkg --print-architecture).asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && apt-get purge -y --auto-remove ca-certificates wget

# make the "en_US.UTF-8" locale so freeswitch will be utf-8 enabled by default
RUN apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

# https://files.freeswitch.org/repo/deb/freeswitch-1.*/dists/jessie/main/binary-amd64/Packages

ENV FS_MAJOR debian-unstable

RUN sed -i "s/jessie main/jessie main contrib non-free/" /etc/apt/sources.list

# https://freeswitch.org/confluence/display/FREESWITCH/Debian+8+Jessie#Debian8Jessie-InstallingfromDebianpackages

RUN apt-get update && apt-get install -y curl \
    && curl https://files.freeswitch.org/repo/deb/debian/freeswitch_archive_g0.pub | apt-key add - \
    && echo "deb http://files.freeswitch.org/repo/deb/$FS_MAJOR/ jessie main" > /etc/apt/sources.list.d/freeswitch.list \
    && apt-get purge -y --auto-remove curl

RUN apt-get update && apt-get install -y freeswitch-all \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

COPY docker-entrypoint.sh /
# Add anything else here

##

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["freeswitch"]