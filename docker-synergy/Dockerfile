FROM fedora:23

MAINTAINER Jon Jozwiak

RUN dnf -y install cmake python make gcc-c++ libX11-devel libXtst-devel libXext-devel libXinerama-devel libcurl-devel qt-devel avahi-compat-libdns_sd-devel openssl-devel rpm-build rpmlint git && dnf clean all

ENV PATH $PATH:/usr/lib64/qt4/bin:/usr/lib/qt4/bin
ENV SYN_VERSION v1.8.2

RUN mkdir -p /usr/src 

RUN cd /usr/src && git clone https://github.com/symless/synergy.git

ENV PATH $PATH:/usr/lib64/qt4/bin:/usr/lib/qt4/bin

WORKDIR /usr/src/synergy

RUN cd /usr/src/synergy && git checkout $SYN_VERSION && ./hm.sh conf -g1 && ./hm.sh build 
RUN cd /usr/src/synergy && ./hm.sh package rpm
