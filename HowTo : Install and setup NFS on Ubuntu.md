![alt Avatar](https://avatars0.githubusercontent.com/u/24376156?v=4&s=100)
# Install and setup NFS on Ubuntu
## Objectives

- Provide NFS export __quickly__ on Ubuntu.

## Environment

- Ubuntu 16.04 (LTS)

## Install packages

```ssh
sudo apt-get install nfs-kernel-server 
```

## Setup

- create export folder
```ssh
sudo mkdir -R /export/ko4la
```

- Bind the folder
```ssh
sudo mount --bind /home/ko4la /export/ko4la
```

- Edit /etc/default/nfs-kernel-server and change NEED_SVCGSSD parameter

```ssh
sudo vi /etc/default/nfs-kernel-server
>>> NEED_SVCGSSD="no"
```

- Define export
```ssh
/export       192.168.1.0/24(rw,fsid=0,no_subtree_check,sync)
/export/ko4la 192.168.1.0/24(rw,nohide,insecure,no_subtree_check,sync)
```

- Restart NFS services
```ssh
sudo service nfs-kernel-server restart
sudo service idmapd restart 
```

- Setup firewall to allow NFS

- Test

There are many many docs to mount NFS export on Linux... Google is our friend :D

On Windows, yeeeaaa I know....., we can install NFS Client and use CLI to mount a drive :
```ssh
mount -o casesensitive=yes -u:ko4la -p:<password> <IPServeurNFS>:/export/ko4la r:
```
