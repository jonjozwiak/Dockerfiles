FROM fedora
MAINTAINER http://fedoraproject.org/wiki/Cloud

# xhost +local:root
# docker run --rm -it -v /dev/snd:/dev/snd -v /tmp/.X11-unix:/tmp/.X11-unix:ro -v /dev/shm:/dev/shm -v /etc/machine-id:/etc/machine-id -e DISPLAY=$DISPLAY -v /dev/dri:/dev/dri --privileged firefox

# Install the appropriate software
RUN yum -y update && yum clean all
RUN yum -y install firefox \
mesa-libGL \
mesa-dri-drivers \
dbus-glib \
curl \
bzip2 \
ca-certificates \
hicolor-icon-theme \
alsa-lib \
dejavu-sans-fonts \
dejavu-serif-fonts \
tar \
gnash-plugin \ 
&& yum clean all

# Install flash
RUN yum -y install http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm && yum -y install flash-plugin && yum clean all 

# Install files for Bluejeans
COPY bluejeans.repo /etc/yum.repos.d/bluejeans.repo
RUN yum -y install bjnplugin && yum clean all

# Install software for WebEx -- not quite working as of yet
RUN yum -y install icedtea-web \
java-1.8.0-openjdk \
pangox-compat.i686 \
libXmu.i686 \
libgcj.i686 \
mesa-libEGL.i686 \
gtk2.i686 \
libpng2.i686 \
&& yum clean all 
## Note, selinux not enabled in container so this is not needed:
## RUN setsebool -P unconfined_mozilla_plugin_transition=off mmap_low_allowed=on

ENTRYPOINT [ "/usr/bin/firefox", "-no-remote" ]
