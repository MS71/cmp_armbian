# Car Media Player Armbian

THis project is an armbian for for my small Banana PI M2+ Zero Car Media Player

# Desired Functionality

- Audio streaming via Bluetooth to an A2DP sink
- Audio streaming via Bluetooth MTP Gadget
- Audio streaming via USB MTP Gadget
- Tests with hight speed I2S driver

# Hardware

- Allwinner H2+ on Banana Pi Zero
- http://www.banana-pi.org/bpi-zero.html

# Requirements

## CuteCom
- sudo chmod a+rw /dev/ttyUSB0
- sudo usermod -a -G dialout userName
- sudo apt-get install cutecom

## MiniCom
- sudo apt-get install minicom

# History

- https://github.com/armbian/build.git forked
- BPI-files/others/armbian/build directory merged from https://github.com/BPI-SINOVOIP/BPI-files.git
- 
- ...

# Build Procedure

**Execution**

	apt-get -y install git
	git clone https://github.com/MS71/cmp_armbian.git
	cd cmp_armbian
        ./compile.sh BOARD=bananapim2zero BRANCH=next KERNEL_ONLY=no INSTALL_HEADERS=yes RELEASE=xenial BUILD_DESKTOP=no
	...
	sudo dd if=output/images/Armbian_5.41_Bananapim2zero_Ubuntu_xenial_next_4.14.27.img of=/dev/[your sdcard device] bs=8192
	sync
	=> insert sdcard
	=> open minicim connected to serial port (e.g. /dev/ttyUSB0)
	=> boot system
	=> login user=root password=1234
	=> follow instructions
	armbian-config
	=> connect to WIFI
	=> login with ssh root@bananapim2zero
	apt-get update
	apt-get upgrade

# Runtime Configuration

- cd rootfs
- scp -r * root@bananapim2zero:/

## Bluetooth Audio via A2DP ...
- install image, change root password
- ... connect to internet ...
- sudo apt-get update
- sudo apt-get upgrade
- apt-get install bluez-tools bluez bluez-hcidump libbluetooth-dev
- apt-get install pkg-config libasound2-dev libglib2.0-dev libsbc-dev libfdk-aac-dev 
=> uncomment AutoEnable=true in /etc/bluetooth/main.conf
- systemctl enable bluetooth.service 
- git clone https://github.com/Arkq/bluez-alsa.git
- cd bluez-alsa
-   autoreconf --install
-   ./configure --enable-aac
-   make && make install
#- exit /etc/init.d/ap6212-bluetooth change next line
#-  => "hciattach /dev/$PORT bcm43xx 115200 flow bdaddr $MAC_OPTIONS" 
#-  to "hciattach /dev/$PORT bcm43xx 1500000 flow bdaddr $MAC_OPTIONS"
- reboot


















# Origional Armbian README

Ubuntu and Debian images for ARM based single-board computers
https://www.armbian.com

## How to build my own image or kernel?

Supported build environments:

- [Ubuntu Xenial 16.04 x64](http://archive.ubuntu.com/ubuntu/dists/xenial-updates/main/installer-amd64/current/images/netboot/mini.iso) guest inside a [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or other virtualization software,
- [Ubuntu Xenial 16.04 x64](http://archive.ubuntu.com/ubuntu/dists/xenial-updates/main/installer-amd64/current/images/netboot/mini.iso) guest managed by [Vagrant](https://www.vagrantup.com/). This uses Virtualbox (as above) but does so in an easily repeatable way. Please check the [Armbian with Vagrant README](https://docs.armbian.com/Developer-Guide_Using-Vagrant/) for a quick start HOWTO,
- [Ubuntu Xenial 16.04 x64](http://archive.ubuntu.com/ubuntu/dists/xenial-updates/main/installer-amd64/current/images/netboot/mini.iso) inside a [Docker](https://www.docker.com/), [systemd-nspawn](https://www.freedesktop.org/software/systemd/man/systemd-nspawn.html) or other container environment [(example)](https://github.com/igorpecovnik/lib/pull/255#issuecomment-205045273). Building full OS images inside containers may not work, so this option is mostly for the kernel compilation,
- [Ubuntu Xenial 16.04 x64](http://archive.ubuntu.com/ubuntu/dists/xenial-updates/main/installer-amd64/current/images/netboot/mini.iso) running natively on a dedicated PC or a server (**not** recommended unless you build kernel only, for full OS images always use virtualization as outlined above),
- **20GB disk space** or more and **2GB RAM** or more available for the VM, container or native OS,
- superuser rights (configured `sudo` or root access).

**Execution**

	apt-get -y install git
	git clone https://github.com/armbian/build
	cd build
	./compile.sh

Make sure that full path to the build script does not contain spaces.

You will be prompted with a selection menu for a build option, a board name, a kernel branch and an OS release. Please check the documentation for [advanced options](https://docs.armbian.com/Developer-Guide_Build-Options/) and [additional customization](https://docs.armbian.com/Developer-Guide_User-Configurations/).

Build process uses caching for the compilation and the debootstrap process, so consecutive runs with similar settings will be much faster.

## Reporting issues

Please read [this](https://github.com/igorpecovnik/lib/blob/master/.github/ISSUE_TEMPLATE.md) notice first before opening an issue.

## More info:

- [Documentation](https://docs.armbian.com/Developer-Guide_Build-Preparation/)
- [Prebuilt images](https://www.armbian.com/download/)
- [Support forums](https://forum.armbian.com/ "Armbian support forum")
- [Project at Github](https://github.com/igorpecovnik/lib)
