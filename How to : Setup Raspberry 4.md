# Before to go

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
* sudo apt update
* sudo apt full-upgrade
* sudo rpi-update
* sudo rpi-eeprom-update -d -a
* sudo raspi-config
   * Boot options > Boot ROM option > Latest > No
   * Boot options > Boot order > USB Boot > OK
   * Finish
   * No
> https://jamesachambers.com/raspberry-pi-4-ubuntu-20-04-usb-mass-storage-boot-guide/

# First Boot
* connect SSD and check
* reboot on SSD
* finish interactive setup
* Update
sudo apt update
sudo apt upgrade
* install ssh
```ssh
sudo apt-get install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

# Keyboard issue

```ssh
echo "blacklist hid_logitech_dj" >> /etc/modprobe.d/local-dontload.conf
echo "install hid_logitech_dj /bin/false" >> /etc/modprobe.d/local-dontload.conf
depmod -a
update-initramfs -u
reboot
```
> https://www.raspberrypi.org/forums/viewtopic.php?t=310293

# Video performance
```ssh
cat /proc/device-tree/soc/firmwarekms@7e600000/status
disabled
cat /proc/device-tree/v3dbus/v3d@7ec04000/status
okay
sudo cp /boot/firmware/config.txt /boot/firmware/config.bak
```
dtoverlay=vc4-kms-v3d-pi4

# Install Pi Apps

Pi Apps allow you to install interesting apps that are not in the repository.
```ssh
wget -qO- https://raw.githubusercontent.com/Botspot/pi-apps/master/install | bash
```
> https://github.com/Botspot/pi-apps

# Update
```ssh
sudo apt update
sudo apt upgrade
```
# Add tool
```ssh
sudo apt install raspi-config tasksel
```



