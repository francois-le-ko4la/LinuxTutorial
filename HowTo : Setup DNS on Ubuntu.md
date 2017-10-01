![alt Avatar](https://avatars0.githubusercontent.com/u/24376156?v=4&s=100)
# How to setup DNS on Ubuntu

In general, the DNS is set up via DHCP. This allows a laptop to connect whatever the connection (home, starbuck, ...). In the case of equipment that does not move, DHCP is not always a good idea. For example, a DNS based on the Internet provider becomes a security flaw in setting up a VPN ...
Our goal is to keep the DHCP that allows us to assign the IP but to freeze the DNS.

## Basic Methodology

- Edit the /etc/resolv.conf file
- Add "nameserver XXX.XXX.XXX.XXX" at the end of the file

> Simple, and effective as usual.<br />

## New methodology (Ubuntu 17.04)

> Canonical changed the way to set the DNS server .... GRRRRrrr ....<br />
> Certainly for very good reasons since before it was simple and effective :)<br />
> Basicaly, the goal is to simplify the system management. Ubuntu user can change all the settings with the Network Manager.

The service /lib/systemd/system/systemd-resolved-update-resolvconf.service causes resolvconf to add 127.0.0.53 by default to the set of nameservers in /etc/resolv.conf.
Each reboot changes the /etc/resolv.conf file. As a consequence, there is no need to insist.

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

- This IP is a link to systemd-resolved daemon....
- View /run/systemd/resolve/resolv.conf :
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
- We find our DNS config.... but "This file is managed by man:systemd-resolved(8). Do not edit."
> Pfff....
- View /etc/systemd/resolved.conf :

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
- This is a nice way but there is another one...
- Ubuntu uses NetWork Manager. Therefore, we can take a look on /etc/NetworkManager

```ssh
$ sudo cat /etc/NetworkManager/system-connections/Connexion\ filaire\ 1
[connection]
id=Connexion filaire 1
uuid=6e65d840-4a55-30fa-9d45-8f48da903f59
type=ethernet
autoconnect-priority=-999
permissions=
timestamp=1506839373

[ethernet]
cloned-mac-address=EC:A8:6B:FD:7B:22
mac-address=EC:A8:6B:FD:7B:22
mac-address-blacklist=

[ipv4]
dns=8.8.8.8;
dns-search=
ignore-auto-dns=true
method=auto

[ipv6]
addr-gen-mode=stable-privacy
dns-search=
method=auto
```

- Use nmcli to setup your DNS !!!!
```ssh
nmcli connection edit __double tab to list available connections and chose appropriate__

   nmcli> remove ipv4.dns  
   nmcli> set ipv4.ignore-auto-dns yes
   nmcli> set ipv4.dns 8.8.8.8 8.8.4.4 (or other dns servers)      
   nmcli> save
   nmcli> quit 
```

- Restart your network

```ssh
nmcli connection down your_connection_name
nmcli connection up your_connection_name
```

- check your DNS 
```ssh
sudo systemd-resolve --status
sudo cat /run/systemd/resolve/resolv.conf
```

> All seems to be good.


## I don't want it ! Because....

Because.... :
- ... systemd provide nice issue and we want a correct name resolution........ just do it...... pleeeeease
- ... my scripts are using /etc/resolv.conf

```ssh
ls -la /etc/resolv.conf
sudo rm -f /etc/resolv.conf
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
```
Quick but not really recommended on desktop/end-user Environment.
