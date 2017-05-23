# docker-openstack-ansible

This is a docker image for Ansible 2.1's OpenStack modules.  This was mainly built because Fedora 24 is the first release to include a python shade RPM.  However, I soon learned that python2-shade 1.4 has some bugs and it wasn't really usable.  So I switched the container to a pip install shade which gives version 1.9.

## Build the container

```
git clone https://github.com/jonjozwiak/Dockerfiles.git
cd Dockerfiles/docker-openstack-ansible
```

Ansible requires SSH keys to access hosts.  You could create new keys with ssh-keygen.  In the case of this container, I'm taking pre-existing keys and uploading them in the container.  Add your SSH keys to the files directory. They should be named `id_rsa` and `id_rsa.pub`

Now, build the container from the docker-openstack-ansible directory
```
ls files/id_rsa* 
# Validate you have id_rsa and id_rsa.pub.  If not, the build will fail
docker build --rm -t <username>/docker-openstack-ansible .
```

## Run the container
Below we are volume mounting a directory from the host which will contain your ansible inventory and playbooks.  In this case /export/docker-openstack-ansible.  You will want to update this to align with your environment.  

```
# docker run --rm -it -v /export/docker-openstack-ansible:/ansible docker-openstack-ansible
```


Note - A great reference on Shade is here: 
http://developer.openstack.org/draft/firstapp-shade/getting_started.html
