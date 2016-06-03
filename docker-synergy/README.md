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

Next we need to setup systemd unit files to enable starting on boot.  Note the file is based on user (thus the synergys@)
```
# Create a Synergy Server systemd file
sudo su - 
cat << EOF > /lib/systemd/system/synergys@.service
[Unit]
Description=Synergy for sharing mouse and keyboard
After=network.target

[Service]
ExecStart=/usr/bin/synergys -f -d WARNING -c /etc/synergy.conf -l /var/log/synergy.log --enable-crypto
ExecStop=/bin/kill -WINCH \${MAINPID}
User=%i

[Install]
WantedBy=multi-user.target
EOF
exit

# As your user logging into the desktop
sudo systemctl daemon-reload
sudo systemctl enable synergys@$(whoami)
sudo systemctl start synergys@$(whoami)
sudo systemctl status synergys@$(whoami)
sudo systemctl stop synergys@$(whoami)

# Create a Synergy Client systemd file
### Note that the host name I'm connecting to 'nuc' must be resolvable.  Also, it's hard coded.  There might be a better way to do this...

# Validate your display matches what is below in the start command
echo $DISPLAY

sudo su -
cat << EOF > /lib/systemd/system/synergyc@.service
[Unit]
Description=Synergy for sharing mouse and keyboard
After=network.target

[Service]
###Don't run in foreground.  It hangs over time...
###ExecStart=/usr/bin/synergyc -f -d WARNING -l /var/log/synergy.log --enable-crypto --display :1 nuc 
# Run as daemon is more stable
Type=oneshot
RemainAfterExit=True
ExecStart=/usr/bin/synergys -d WARNING -c /etc/synergy.conf -l /tmp/synergy.log --enable-crypto
User=%i

[Install]
WantedBy=multi-user.target
EOF
exit

# As your user logging into the desktop
sudo systemctl daemon-reload
sudo systemctl enable synergyc@$(whoami)
sudo systemctl start synergyc@$(whoami)
sudo systemctl status synergyc@$(whoami)

```

At this point things should be functioning properly on server and client now and at boot time.  Keep in mind if the server is down when the client boots, it will not connect properly... 

Overall I dislike the client startup script.  Mainly I don't like the hardcoded host name and display.  However, I've not found a quick alternative.

## References
* https://www.mattcutts.com/blog/how-to-configure-synergy-in-six-steps/
* http://www.thegeekstuff.com/2014/03/synergy-share-keyboard-mouse/
* https://jackiechen.org/2014/10/24/configure-synergy-as-systemd-service/
* https://hub.docker.com/r/sebgregoire/synergy/
