# syntax=docker/dockerfile:1.3.0-labs

## You can change these variables
ARG LANG=en_US.UTF-8
ARG LANGUAGE=en_US

##################################################
###> Do NOT change anything beyond this point <###
##################################################

FROM kalilinux/kali-rolling:latest AS base

LABEL org.label-schema.name='reconftw'
LABEL org.label-schema.description='A simple script for full recon'
LABEL org.label-schema.usage='https://github.com/cosmin91ro/reconftw'
LABEL org.label-schema.url='https://github.com/cosmin91ro/reconftw'
LABEL org.label-schema.docker.cmd.devel='docker run --rm -ti cosmin91ro/reconftw'
LABEL MAINTAINER="cwave"

ARG LANG
ARG LANGUAGE

ENV LANG=$LANG
ENV LANGUAGE=$LANGUAGE
ENV LC_ALL=$LANG

ENV DEBIAN_FRONTEND=noninteractive
ENV DEBCONF_NONINTERACTIVE_SEEN=true

ENV GOPATH=$HOME/go
ENV GOROOT=/usr/local/go
ENV PATH=$GOPATH/bin:$GOROOT/bin:$PATH

COPY 01_nodoc /etc/dpkg/dpkg.cfg.d/

RUN <<eot
#!/bin/bash
set -x
############> Update Sources <############
echo "deb http://kali.download/kali kali-rolling main contrib non-free" > /etc/apt/sources.list
echo "deb-src http://kali.download/kali kali-rolling main contrib non-free" >> /etc/apt/sources.list
############> System Configuration <############
apt clean all
apt update
apt full-upgrade -f -y --allow-downgrades
apt install -y --no-install-recommends apt-utils ca-certificates git nano wget
############> Congifure Locales <############
apt install -y --no-install-recommends locales
sed -i -- "/${LANG}/s/^# //g" /etc/locale.gen
dpkg-reconfigure locales
update-locale LANG=${LANG}
############> Install reconFTW <############
mkdir -p /root/Tools
cd /root/Tools
git clone https://github.com/cosmin91ro/reconftw.git
cd reconftw
./install.sh
############> Clean up <############
apt update
apt remove --purge -y build-essential
apt autoremove -y
apt install -y --no-install-recommends localepurge
sed -i -- '/^USE_DPKG/s/^/#/' /etc/locale.nopurge
dpkg-reconfigure localepurge
localepurge
apt clean all
find /var/cache -type f -delete
find /var/lib/apt/lists -type f -delete
find /var/log -type f -delete
rm -rf /root/.cache
rm -rf /root/go
eot

ARG SHODAN_API_KEY
RUN shodan init $SHODAN_API_KEY

WORKDIR /root/Tools/reconftw
ENTRYPOINT [ "bash", "reconftw.sh" ]
CMD [ "--help" ]
