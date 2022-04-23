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

#WARNING: 'edid-decode' package is not installed
#Probe for hardware ... Ok
#Reading logs ... WARNING: X11-related logs are not collected (try to run 'xhost +local:' to enable access or run as root by su)
#Ok
#Uploaded to DB, Thank you!
#
Probe URL: https://linux-hardware.org/?probe=e4d563d75b
```

```shell
# install zerotier
#   (https://www.zerotier.com/download/#downloadLinux)

curl -s 'https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg' | gpg --import && \
if z=$(curl -s 'https://install.zerotier.com/' | gpg); then echo "$z" | sudo bash; fi

#...
#
#*** Waiting for identity generation...
#
#*** Success! You are ZeroTier address [ 1234567890 ].

sudo zerotier-cli info

#200 info 1234567890 1.8.7 ONLINE

sudo zerotier-cli info

#200 info 1234567890 1.8.7 ONLINE

sudo zerotier-cli join 1234567890123456

#200 join OK

# access via web browser https://my.zerotier.com/network/1234567890123456
#     and authorize 1234567890

# after, come back to terminal and see if the zerotier interface got a ip address

ifconfig zt12345678

#zt12345678: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 2800
#        inet 192.168.191.36  netmask 255.255.255.0  broadcast 192.168.191.255
#        inet 192.168.191.1  netmask 255.255.255.0  broadcast 192.168.191.255
#        inet6 fdaf:1234:1234:1234:1234:1234:1234:1234  prefixlen 88  scopeid 0x0<global>
#        inet6 fe80::1234:1234:1234:1234  prefixlen 64  scopeid 0x20<link>
#        ether 5a:12:12:12:12:12  txqueuelen 1000  (Ethernet)
#        RX packets 14  bytes 6790 (6.6 KiB)
#        RX errors 0  dropped 0  overruns 0  frame 0
#        TX packets 46  bytes 6173 (6.0 KiB)
#        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

```shell
# install raspap 
#   (https://raspap.com/#quick)

curl -sL https://install.raspap.com | bash

#[ ✓ ok ]
#RaspAP Install: Installation completed
#Join RaspAP Insiders for early access to exclusive features!
#
#> https://docs.raspap.com/insiders/
#> https://github.com/sponsors/RaspAP/
#
#The system needs to be rebooted as a final step. Reboot now? [y/N]: y
```
