# Beginning

* Rasp PI4B 8GB
* 1 SSD / USB3
* 1 SD CARD
* 1 cable (HDMI/microHDMI)
* 1 SD Card reader
* 1 laptop with Raspberry Pi Imager

# Raspberry Pi Imager
* install Raspberry Pi OS Lite on SD card using imager
* install Ubuntu on SSD card using imager

# Update the PI4
* Boot on SD card
* Login
* update
```console
sudo apt update
sudo apt full-upgrade
sudo rpi-update
sudo rpi-eeprom-update -d -a
sudo raspi-config
```
> Boot options > Boot ROM option > Latest > No
> Boot options > Boot order > USB Boot > OK
> Finish
> No
> https://jamesachambers.com/raspberry-pi-4-ubuntu-20-04-usb-mass-storage-boot-guide/

# First Boot on SSD
* connect SSD and check the storage
* reboot on SSD
* finish interactive setup (dont update)

# SSH
```console
sudo apt-get install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

# Update Ubuntu
```console
sudo apt update
sudo apt upgrade
```

# Keyboard issue (Logitech Wireless)

```console
echo "blacklist hid_logitech_dj" >> /etc/modprobe.d/local-dontload.conf
echo "install hid_logitech_dj /bin/false" >> /etc/modprobe.d/local-dontload.conf
depmod -a
update-initramfs -u
reboot
```
> https://www.raspberrypi.org/forums/viewtopic.php?t=310293

# Video performance
```console
cat /proc/device-tree/soc/firmwarekms@7e600000/status
disabled
cat /proc/device-tree/v3dbus/v3d@7ec04000/status
okay
sudo cp /boot/firmware/config.txt /boot/firmware/config.bak
```
```
dtoverlay=vc4-kms-v3d
gpu_mem=256
```

# TRIM
## Issue with USB Storage
* check SSD :
```console
sudo hdparm -I /dev/sda | grep TRIM
           *    Data Set Management TRIM supported (limit 8 blocks)
```
* check TRIM
```console
sudo fstrim -v /
fstrim: /: the discard operation is not supported
```
Here, we have an issue with TRIM on USB storage.
usefull link : https://www.glump.net/howto/desktop/enable-trim-on-an-external-ssd-on-linux

## Fix
To solve this issue we list the USB device:
```console
lsusb
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 002: ID 174c:235c ASMedia Technology Inc. Ugreen Storage Device
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 003: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
My ProductId/VendorID are 174c:235c.
We create a udev rule (/etc/udev/rules.d/50-usb-ssd-trim.rules) to enable TRIM:
```console
ACTION=="add|change", ATTRS{idVendor}=="174c", ATTRS{idProduct}=="235c", SUBSYSTEM=="scsi_disk", ATTR{provisioning_mode}="unmap"
```
Fixe the /etc/fstab:
```console
# UNCONFIGURED FSTAB FOR BASE SYSTEM
LABEL=writable    /     ext4    defaults,noatime,discard,x-systemd.growfs    0 0
LABEL=system-boot       /boot/firmware  vfat    defaults        0       1
tmpfs /var/cache/apt/archives tmpfs defaults,noexec,nosuid,nodev,mode=0755 0 0
```
Reboot and test again:
```console
sudo fstrim -v /
/: 54.9 GiB (58915901440 bytes) trimmed
```

## TRIM service

* Check service
```console
systemctl status fstrim.timer
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
systemctl status fstrim.timer
```

# Disable Bluetooth

```console
sudo sed -i 's/AutoEnable=.*/AutoEnable=false/' /etc/bluetooth/main.conf
```

# Install Pi Apps (optional)

Pi Apps allow you to install interesting apps that are not in the repository.
```console
wget -qO- https://raw.githubusercontent.com/Botspot/pi-apps/master/install | bash
```
> https://github.com/Botspot/pi-apps

# Add raspi-config 
```console
sudo apt install raspi-config
```

# Other
```console
sudo apt install ubuntu-restricted-extras tasksel
```

> ubuntu-restricted-extras.... yeeeees I know...... Bad.

# Unattended upgrade
## Setup unattended upgrade
```console
sudo sed -i 's=//Unattended-Upgrade::Mail .*=Unattended-Upgrade::Mail "XXX@XXX.fr";=' /etc/apt/apt.conf.d/50unattended-upgrades
sudo sed -i 's=//Unattended-Upgrade::MailReport .*;=Unattended-Upgrade::MailReport "on-change";=' /etc/apt/apt.conf.d/50unattended-upgrades
sudo sed -i 's=//Unattended-Upgrade::Remove-Unused-Kernel-Packages .*;=Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";=' /etc/apt/apt.conf.d/50unattended-upgrades
sudo sed -i 's=//Unattended-Upgrade::Remove-Unused-Dependencies .*;=Unattended-Upgrade::Remove-Unused-Dependencies "true";=' /etc/apt/apt.conf.d/50unattended-upgrades
sudo sed -i 's=//Unattended-Upgrade::Automatic-Reboot .*;=Unattended-Upgrade::Automatic-Reboot "true";=' /etc/apt/apt.conf.d/50unattended-upgrades
sudo sed -i 's=//Unattended-Upgrade::Automatic-Reboot-WithUsers .*;=Unattended-Upgrade::Automatic-Reboot-WithUsers "true";=' /etc/apt/apt.conf.d/50unattended-upgrades
sudo sed -i 's=//Unattended-Upgrade::Automatic-Reboot-Time .*;=Unattended-Upgrade::Automatic-Reboot-Time "04:00";=' /etc/apt/apt.conf.d/50unattended-upgrades
```

## Setup postfix with GMail

* Install postfix
```console
sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules
```
> choose website during the setup and leave the hostname by default

* Enable postfix
```console
sudo systemctl enable postfix
```
* Gmail Setup
```console
sudo sed -i 's/relayhost = .*/relayhost = [smtp.gmail.com]:587/' /etc/postfix/main.cf

sudo tee -a /etc/postfix/main.cf <<EOF
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_CAfile = /etc/postfix/cacert.pem
smtp_use_tls = yes
EOF

sudo tee -a /etc/postfix/sasl_passwd  <<EOF
[smtp.gmail.com]:587 XXX@gmail.com:AppPassword
EOF

sudo chmod 400 /etc/postfix/sasl_passwd
sudo postmap /etc/postfix/sasl_passwd
cd /etc/ssl/certs
sudo openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key-for-smtp-gmail.pem -out cert-for-smtp-gmail.pem
cat /etc/ssl/certs/cert-for-smtp-gmail.pem | sudo tee -a /etc/postfix/cacert.pem
```
* Reload and test
```console
sudo /etc/init.d/postfix reload
sudo /etc/init.d/postfix status
sudo echo "Test mail from postfix" | mail -s "Test Postfix" adresse@mail.com
```

# Chrome
chrome://flags/#ignore-gpu-blocklist - enabled
chrome://flags/#enable-gpu-rasterization - enabled
chrome://flags/#enable-accelerated-video-decode - missing in flags, but after restart Chromium chrome://gpu all settings is green (enabled) except Vulkan.
add h264ify plungin
chromium --use-gl=desktop on GNOME/Wayland.
chromium --use-gl=egl on GNOME/Xorg.
--enable-features=VaapiVideoDecoder
--disk-cache-dir="/media/ramdisk/"

# FF
```console
in about:config
gfx.webrender.all=true
gfx.webrender.force-disabled=false
layers.acceleration.force-enabled=true
layers.gpu-process.enabled=true
media.gpu-process-decoder=true
dom.webgpu.enabled=true
media.ffmpeg.vaapi.enabled
media.ffmpeg.vaapi-drm-display.enabled=true
layers.omtp.enabled true
media.navigator.mediadatadecoder_vpx_enabled=true
media.ffvpx.enabled=true
gfx.x11-egl.force-enabled=false
gfx.x11-egl.force-disabled=true
# ? gfx.x11-egl=true
```

# Overclock (PI 4B 8GB)

```console
sudo tee -a /boot/firmware/config.txt <<EOF
arm_freq=2000
over_voltage=6
EOF
```

# Reboot & check
```console
sudo reboot
```

# Conclusion
This document is a quick summary and allow me to track important things. In the real life I prefer to use ansible in order to apply the config on different Raspberry : https://github.com/francois-le-ko4la/ansible-raspberry
