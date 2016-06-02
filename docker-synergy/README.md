# Docker-Synergy

Docker container used to facilitate building Synergy RPMs from source.

## Build
git clone .... 
cd docker-synergy
docker build --rm -t synergy .


## Get the RPM 
```
docker run -it synergy --name synergy /bin/bash

cd /usr/src/synergy/bin
ls *.rpm

scp synergy-v1.8.2-beta-8da57cd-Linux-x86_64.rpm root@xxx.xxx.xxx.xxx:/tmp

exit
docker rm synergy

```

## Install and Configure Synergy
All of that was just prep work to get the RPM.  Now you can actually use it to configure Synergy.  

Synergy works in a client/server fashion.  The server (where your keyboard and mouse are actually connected) allows others to connect and use.  

```
# On your hosts: 
dnf -y install synergy-v1.8.2-beta-8da57cd-Linux-x86_64.rpm

# Create the config file on the server which hosts your keyboard/mouse
### In my case, 2 hosts.  lenovo and nuc.  nuc has the keyboard/mouse and will be the server.  lenovo screen is on the left.  nuc screen is on the right
cat << EOF > /etc/synergy.conf
section: screens
lenovo:
nuc:
end

section: aliases
lenovo:
lenovo.example.com
192.168.1.20
nuc:
nuc.example.com
192.168.1.10
end

section: links 
lenovo:
right = nuc 
nuc: 
left = lenovo
end

section: options
screenSaverSync = false
keystroke(f12) = lockCursorToScreen(toggle)
end
EOF

chmod 644 /etc/synergy.conf

# Test the config by running Synergy Server in the foreground
synergys -f --config /etc/synergy.conf

# Connect Clients
synergyc -f nuc
```


## References
* https://www.mattcutts.com/blog/how-to-configure-synergy-in-six-steps/
* http://www.thegeekstuff.com/2014/03/synergy-share-keyboard-mouse/
* https://hub.docker.com/r/sebgregoire/synergy/
