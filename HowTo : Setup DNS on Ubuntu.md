# How to setup DNS on Ubuntu (DRAFT)

In general, the DNS is set up via DHCP. This allows a laptop to connect whatever the connection (home, starbuck, ...). In the case of equipment that does not move, DHCP is not always a good idea. For example, a DNS based on the Internet provider becomes a security flaw in setting up a VPN ...
Our goal is to keep the DHCP that allows us to assign the IP but to freeze the DNS.

## Basic Methodology (Ubuntu 16.04)

- Edit the /etc/resolv.conf file
- Add "nameserver XXX.XXX.XXX.XXX" at the end of the file

> Simple, and effective as usual.

## New methodology (Ubuntu 17.04)

Canonical changed the way to set the DNS server .... GRRRRrrr ....
Certainly for very good reasons since before it was simple and effective :)

The script /lib/systemd/system/systemd-resolved.service.d/resolvconf.conf causes resolvconf to add 127.0.0.53 by default to the set of nameservers in /etc/resolv.conf.
Each reboot changes the /etc/resolv.conf file so there is no need to insist.

```ssh
cat /lib/systemd/resolv.conf
# This is a static resolv.conf file for connecting local clients to
# systemd-resolved via its DNS stub listener on 127.0.0.53.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 127.0.0.53
```
This IP is a link to systemd-resolved daemon....

```ssh
cat /run/systemd/resolve/resolv.conf
# This file is managed by man:systemd-resolved(8). Do not edit.
#
# This is a dynamic resolv.conf file for connecting local clients directly to
# all known DNS servers.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 8.8.8.8
```
We find our DNS config....
Pfff....

Ha! We have found the config file :

```ssh
cat /etc/systemd/resolved.conf
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See resolved.conf(5) for details

[Resolve]
#DNS=
#FallbackDNS=
#Domains=
#LLMNR=yes
#MulticastDNS=yes
#DNSSEC=no
#Cache=yes
#DNSStubListener=udp
```

### I don't want it ! Because....

Because systemd provide nice issue and we want a correct name resolution........ just do it...... pleeeeease
```ssh
ls -la /etc/resolv.conf
sudo rm -f /etc/resolv.conf
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
```
Quick but not really recommended. Ubuntu 17.10 seems to be more efficient. Our tests are fine but ....

To be continued.....

