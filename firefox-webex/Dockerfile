FROM fedora:23
MAINTAINER Jon Jozwiak (jjozwiak@redhat.com)

# xhost +local:root
# docker run --rm -it -v /dev/snd:/dev/snd -v /tmp/.X11-unix:/tmp/.X11-unix:ro -v /dev/shm:/dev/shm -v /etc/machine-id:/etc/machine-id -e DISPLAY=$DISPLAY -v /dev/dri:/dev/dri --privileged firefox-webex

# References:
# http://negativo17.org/enabling-cisco-webex-in-fedora-19-x86_64-and-i686/
# https://at.projects.genivi.org/wiki/display/TOOL/WebEx+on+Linux

# Install the appropriate software
RUN dnf -y update && dnf clean all
RUN dnf -y install firefox \
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
&& dnf clean all

# Install flash
RUN dnf -y install http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm && dnf -y install flash-plugin && dnf clean all 

# Install software for WebEx
RUN dnf -y install icedtea-web \
java-1.8.0-openjdk \
pangox-compat.i686 \
libXmu.i686 \
mesa-libEGL.i686 \
gtk2.i686 \
libpng.i686 \
alsa-lib.i686 \
libXtst.i686 \
libart_lgpl.i686 \
&& dnf clean all 
## Note - libgcj.i686 wasn't found so I skipped it
## Note, selinux not enabled in container so this is not needed:
## RUN setsebool -P unconfined_mozilla_plugin_transition=off mmap_low_allowed=on

## RUN /usr/sbin/alternatives --install /usr/lib64/mozilla/plugins/libjavaplugin.so libjavaplugin.so.x86_64 /usr/lib64/IcedTeaPlugin.so 200000

ENTRYPOINT [ "/usr/bin/firefox", "-no-remote" ]
