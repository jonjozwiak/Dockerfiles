dockerfiles-firefox
==========================

Fedora dockerfile for Firefox with Bluejeans.  Uses host's X-windows session and passes through video, mic, and sound.  

Get the version of Docker

```
# docker version
```

To build:

Copy the sources down -

```
# docker build --rm -t <username>/firefox .
```

To run:

```
# xhost +local:root
# docker run --rm -it -v /dev/snd:/dev/snd -v /tmp/.X11-unix:/tmp/.X11-unix:ro -v /dev/shm:/dev/shm -v /etc/machine-id:/etc/machine-id -e DISPLAY=$DISPLAY -v /dev/dri:/dev/dri --privileged firefox
```


