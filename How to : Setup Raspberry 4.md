install Raspberry Pi OS Lite on SD card using imager
install Ubuntu on SSD card using imager

Boot on SD card
Login
sudo apt update
sudo apt full-upgrade
sudo rpi-update
sudo rpi-eeprom-update -d -a
sudo raspi-config
> Boot options > Boot ROM option > Latest > No
> Boot options > Boot order > USB Boot > OK
> Finish
> No
https://jamesachambers.com/raspberry-pi-4-ubuntu-20-04-usb-mass-storage-boot-guide/

connect SSD and check
reboot on SSD
finish interactive setup
sudo apt update
sudo apt upgrade
sudo apt-get install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
