![alt Avatar](https://avatars0.githubusercontent.com/u/24376156?v=4&s=100)
# Install Ubuntu 16.04 / Intel NUC & Post Install
## Objectives
We give in the following all the steps allowing the Ubuntu installation on Intel NUC PC.
This document is provided “as is” without any representations or warranties, express or implied.

## Environment
We setup Ubuntu 16.04 on Intel NUC (D54250WYK) : 8GB, intel HDA 5XXX, and SSD Crucial M500 (100GB).

## Caveats/Warning
- Unity8 - blackscreen
- Firmware update does not work
- BIOS update does not work

## Requirements
- The NUC
- 1 flash drive
- screen/keyboard/mouse connected
- ISO : Ubuntu Desktop 16.04 (LTS)

## BIOS update
- Download the last BIOS version
- Copy the binary "XXX.BIO" on flash drive
- Power off laptop, power on and press F7 to get in boot menu
- Select the flash drive
- Wait until the end of process

## Minimal Ubuntu installation
we have enough tutorial on many website.... We use a flash drive in order to install the operating system and we do a minimal setup according to this guide : https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-desktop#0

## Disque SSD
### TRIM

- Check noatime/discard parameters in /etc/fstab :
```sh
UUID=XXXXXXXXXXXXXXXXXXX /               ext4    noatime,discard,errors=remount-ro 0       1
```
- Check the TRIM process
```sh
sudo fstrim / -v
[sudo] password for ko4la:

/: 9,5 GiB (10213740544 bytes) trimmed
```
- Create a crontab script
```sh
sudo vi /etc/cron.daily/fstrim
```
- Use this example
```sh
#!/bin/sh
LOG=/var/log/fstrim.log
echo "$(date -R)" >> $LOG
fstrim -v / >> $LOG
```
### APT Cache on a TMPFS folder
In order to protect the SSD, we replace original folder (/var/cache/apt/archives) by a TMPFS drive.
- Edit /etc/fstab
```ssh
sudo vi /etc/fstab
```
- Add the new mountpoint
```ssh
tmpfs /media/virtualram tmpfs defaults,size=512M 0 0
```
- Create the mountpoint and mount the drive
```ssh
sudo mkdir /media/virtualram
sudo mount /media/virtualram
```
- Create a link to the new drive
```
sudo rm -rf /var/cache/apt/archives
sudo ln -s /media/virtualram/ /var/cache/apt/archives
```

### Disable swap
- Edit /etc/fstab
```ssh
sudo vi /etc/fstab
```
- comment the line
```ssh
# UUID=XXXXXXXX none            swap    sw              0       0
```
- Disable the swap
```ssh
sudo swapoff -a
```
We will use the swap space in order to create a recovery space.
### SSD Update
We use a crucial M500 and the update tool need Windows. (http://www.crucial.com/usa/en/support-ssd).
Whereas, Crucial website allow to download Linux ISO to update the disk ???...
Micron website allow to download Linux tools ;-) : Storage Executive 64 bits
According to the documentation we can use this tool on Crucial M500
> 
> source of information :
> http://www.micron.com/products/solid-state-storage/storage-executive-software
> 

## Browser and VOD (netflix/canalplay...)

### Firefox

Installed by default

### Chrome
- Chrome installation
```ssh
echo "deb http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list
sudo wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -
sudo apt-get update
sudo apt-get install google-chrome-stable
```
- Use the virtual drive to store Google Cache
```ssh
mkdir /media/virtualram/GPUCache
mkdir /media/virtualram/google-chrome
rm -rf $HOME/.config/google-chrome/Default/GPUCache
rm -rf $HOME/.cache/google-chrome
ln -s /media/virtualram/GPUCache $HOME/.config/google-chrome/Default/GPUCache
ln -s /media/virtualram/google-chrome $HOME/.cache/google-chrome
```
- Edit ~.profile :
- Add theses lines :
```ssh
mkdir /media/virtualram/GPUCache > /dev/null 2>&1
mkdir /media/virtualram/google-chrome > /dev/null 2>&1
```
>
> Each reboot will flush the virtual drive...
>

## Java 8

Either we use the community version or we use the Oracle version. To avoid compatibility issue, there is a PPA allowing to have the Oracle version:
```ssh
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```
>
> YEEEeeeeaaaa, it's bad.... NNNOOoooooo It's not GNU GPL..... 
>

## Flash
Flash is a super hero that runs very fast. So nothing to do with a Linux installation.
>
> Please, don't use, install, promote... 
>

## Media Center
### Codecs
```ssh
sudo apt-get install ubuntu-restricted-extras libavcodec-extra
```
### Kodi
```ssh
sudo apt-get install kodi
```
>
> we talk about kodi but we think that Plex is better
>

### Plex
```ssh
wget https://downloads.plex.tv/plex-media-server/0.9.16.4.1911-ee6e505/plexmediaserver_0.9.16.4.1911-ee6e505_amd64.deb
dpkg -i plexmediaserver_0.9.16.4.1911-ee6e505_amd64.deb
```

### Caffeine
```ssh
sudo apt-get install caffeine
```

## Security
### Warning
>
> Security Architect is a job. We provide only best practices.
>
### Password management
>
> Don't use online tool to store your password !!!!! Never.
> Use Keepass2.... trust me.....
>
```ssh
sudo apt-get install keepass2
```
>
> source of information : http://keepass.info/
>

### Use disk encryption
Ubuntu installation allow you disk encryption.

### Use HTTPS

use HPPS everywhere with chrome : https://chrome.google.com/webstore/detail/https-everywhere/gcbommkclmclpchllfjekcdonpmejbdp?utm_source=chrome-ntp-icon

### Fail2Ban : secure connections
```ssh
sudo fail2ban-client
```
>
> source of information : https://doc.ubuntu-fr.org/fail2ban
>

### VPN

- install OpenVPN
```ssh
sudo apt-get install openvpn network-manager-openvpn network-manager-openvpn-gnome
```
>
> Setup openvpn according to the VPN Provider.
> Test your security... DNSLeak, IPLeak...
> Other informations : https://doc.ubuntu-fr.org/securite

## Recover partition
- Create a mountpoint
```ssh
sudo mkdir /media/recover
```
- Install/start gparted
```ssh
sudo apt-get install gparted
sudo gparted
```
- Right click on swap partition Format > ext4
- Edit /etc/fstab
```ssh
UUID=XXXXX /media/recover  ext4    noatime,discard,errors=remount-ro 0       1
```
- mount the device and copy iso
```ssh
sudo mount /media/recover
cd /media/recover
sudo wget http://www-ftp.lip6.fr/pub/linux/distributions/Ubuntu/releases/16.04/ubuntu-16.04-desktop-amd64.iso
```
- Edit /etc/default/grub
```ssh
sudo vi /etc/default/grub
```
- Comment "GRUB_HIDDEN_TIMEOUT=0"
- Edit /etc/grub.d/40_custom
```ssh
sudo vi /etc/grub.d/40_custom
```
- Add the menuEntry :
```ssh
menuentry "Ubuntu 16.04LTS ISO" {
insmod loopback
insmod iso9660
set isofile="/ubuntu-16.04-desktop-amd64.iso"
loopback loop (hd0,gpt3)$isofile
linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile noprompt noeject
initrd (loop)/casper/initrd.lz
}
```
>
> change "loopback loop (hd0,gpt3)$isofile" according to your setup.
>
- Update Grub
```ssh
sudo update-grub2
```
- Restart the system
- Use Grub and select "Ubuntu 16.04LTS ISO"
>
> The live CD is loaded and you can make any changes on your system (convenient!)
> This method is convenient in case of worries without having to make a usb key bootable.
> There is a lot of ISO on the net to test the hardware, use gparted,......
> In our case we have an 8GB partition (relative to RAM). The remaining space will allow to store
> different configuration files and thus to completely redo the system from scratch.
> The data part of the user must be saved using NAS / USB disk media.
>
