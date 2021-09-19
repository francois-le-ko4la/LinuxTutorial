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
* update
```ssh
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
```ssh
sudo apt-get install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

# Update Ubuntu
```ssh
sudo apt update
sudo apt upgrade
```

# Keyboard issue (Logitech Wireless)

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

> dtoverlay=vc4-kms-v3d
> gpu_mem=512

# TRIM

* check SSD :
```ssh
sudo hdparm -I /dev/sda | grep TRIM
```

* Check service
```ssh
systemctl status fstrim.timer
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
systemctl status fstrim.timer
```
> Enabled by default on my plateform

# Disable Bluetooth

```ssh
sudo sed -i 's/AutoEnable=.*/AutoEnable=false/' /etc/bluetooth/main.conf
```

# Install Pi Apps

Pi Apps allow you to install interesting apps that are not in the repository.
```ssh
wget -qO- https://raw.githubusercontent.com/Botspot/pi-apps/master/install | bash
```
> https://github.com/Botspot/pi-apps

# Add raspi-config 
```ssh
sudo apt install raspi-config
```

# Other
```ssh
sudo apt install ubuntu-restricted-extras tasksel
```

> ubuntu-restricted-extras.... yeeeees I know...... Bad.

# Unattended upgrade
## Setup unattended upgrade
```ssh
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
```ssh
sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules
```
> choose website during the setup and leave the hostname by default

* Enable postfix
```ssh
sudo systemctl enable postfix
```
* Gmail Setup
```ssh
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
```ssh
sudo /etc/init.d/postfix reload
sudo /etc/init.d/postfix status
sudo echo "Test mail from postfix" | mail -s "Test Postfix" adresse@mail.com
```

# Overclock (PI 4B 8GB)

```ssh
sudo tee -a /boot/firmware/config.txt <<EOF
arm_freq=2000
over_voltage=6
EOF
```

# Reboot & check
```ssh
sudo reboot
```
