# tuto-zerotier-raspap-in-my-rpi3b

Tutorial on how I've installed ZeroTier & RaspAP in my Raspberry Pi 3B & 2 hw adds: tp-link UE330 (Hub USB 3.0 and RJ45 Gigabit)&amp; tp-link Archer T2U Plus (ac600 wi-fi usb)

## Hardware

- [Raspberry Pi 3B](https://www.raspberrypi.com/products/raspberry-pi-3-model-b/)
- [tp-link UE330 (Hub USB 3.0 and RJ45 Gigabit)](https://www.tp-link.com/en/support/download/ue330/)
- [tp-link Archer T2U Plus (ac600 wi-fi usb)](https://www.tp-link.com/en/support/download/archer-t2u-plus/)

## Software

- OS: [Raspberry Pi OS	(32-bit) Lite - Bullseye](https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit)
- Plus One: [ZeroTier - Global Area Networking](https://www.zerotier.com/)
- Plus Two: [RaspAP: The easiest, full-featured wireless router setup for Debian-based devices. Period.](https://raspap.com/)

## Steps

```shell
# update system

sudo apt-get update
sudo apt-get full-upgrade
sudo reboot
```

```shell
# find vendor and id from external hardware
  
#   # usb devices that were currently plugged

lsusb

#
#Bus 001 Device 007: ID 0bda:8153 Realtek Semiconductor Corp. ç Gigabit Ethernet Adapter
#Bus 001 Device 006: ID 2357:0120 TP-Link Archer T2U PLUS [RTL8821AU]
#
#...
#

#   # hw-probe: installation

sudo apt update
sudo apt install hw-probe --no-install-recommends

#   # hw-probe: 1st execution

sudo -E hw-probe -all -upload

#WARNING: 'edid-decode' package is not installed
#Probe for hardware ... Ok
#Reading logs ... WARNING: X11-related logs are not collected (try to run 'xhost +local:' to enable access or run as root by su)
#Ok
#Uploaded to DB, Thank you!
#
Probe URL: https://linux-hardware.org/?probe=99d66ae8c3
```

```shell
# install missing drivers based on microchip of device

#     # wifi (Realtek RTL8821AU)
#         (https://github.com/morrownr/8821au)

sudo apt update && sudo apt upgrade
sudo reboot

sudo apt install -y raspberrypi-kernel-headers bc build-essential dkms git

mkdir -p ~/src
cd ~/src

git clone https://github.com/morrownr/8821au-20210708.git
cd ~/src/8821au-20210708

./ARM_RPI.sh

sudo ./install-driver.sh
lsusb
ifconfig

cd src/8821au-20210708/
sudo ./edit-options.sh
sudo iw reg get
dkms status
sudo shutdown -r now

dkms status
sudo iw reg get
ip a
lsusb
sudo iw reg get
sudo nano /etc/default/crda
sudo iw reg get
sudo iw reg set BR
sudo iw reg get
history

# # gigabit ethernet (Realtek ## wifi (Realtek RTL8153))
```

```shell
#   # hw-probe: 2nd execution

sudo -E hw-probe -all -upload
```

```shell
# install zerotier
#   (https://www.zerotier.com/download/#downloadLinux)

curl -s 'https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg' | gpg --import && \
if z=$(curl -s 'https://install.zerotier.com/' | gpg); then echo "$z" | sudo bash; fi
```

```shell
# install raspap 
#   (https://raspap.com/#quick)

curl -sL https://install.raspap.com | bash
```
