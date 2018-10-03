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

## Bluetooth Audio via A2DP with premade image ...
- install image Armbian_5.41_Bananapim2zero_Ubuntu_xenial_default_3.4.113_desktop.img
- boot and change root password (org rootpasswd=1234)
- ... connect to internet ...
- connect ethernet via usb adapter
- >export TERM=vt100
- armbian-config => install bluetooth support
- >reboot
- >ps -ef | grep blue                                                                                                                      
- root      1062     1  0 17:30 ?        00:00:00 /usr/lib/bluetooth/bluetoothd                                                                                  
- root      2926  2507  0 17:31 ttyS0    00:00:00 grep --color=auto blue                                                                                         
- >bluetoothctl                                                                                                                            
- [NEW] Controller 43:29:B1:55:01:01 bananapim2zero [default]                                                                                                    
- [bluetooth]#
- => bluetooth is working
- login via ssh
- apt-get install pkg-config libasound2-dev libglib2.0-dev libsbc-dev libfdk-aac-dev libtool bluez-tools bluez bluez-hcidump libbluetooth-dev 
- apt-get install gstreamer1.0-tools libgstreamer-plugins-base1.0-0 gstreamer1.0-alsa gstreamer1.0-plugins-base-apps gstreamer1.0-plugins-good
- git clone https://github.com/Arkq/bluez-alsa.git
- cd bluez-alsa
-   autoreconf --install
-   ./configure --enable-aac
-   make && make install
- change baudrate from 115200 to 1500000 in /etc/init.d/ap6212-bluetooth

- 1.) terminal
- >bluetoothctl
- [NEW] Controller 43:29:B1:55:01:01 bananapim2zero [default]
- [bluetooth]# agent on
- Agent registered
- [bluetooth]# scan on
- Discovery started
- [CHG] Controller 43:29:B1:55:01:01 Discovering: yes
- [NEW] Device B8:D5:0B:C4:80:C7 JBL Charge 3
- [bluetooth]# pair B8:D5:0B:C4:80:C7
- Attempting to pair with B8:D5:0B:C4:80:C7
- [CHG] Device B8:D5:0B:C4:80:C7 Class: 0x240414
- [CHG] Device B8:D5:0B:C4:80:C7 Icon: audio-card
- [CHG] Device B8:D5:0B:C4:80:C7 Connected: yes
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 00001101-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 00001108-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 0000110b-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 0000110c-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 0000110e-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 0000111e-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
- [CHG] Device B8:D5:0B:C4:80:C7 Paired: yes
- Pairing successful
- [CHG] Device B8:D5:0B:C4:80:C7 Connected: no
- [bluetooth]# connect B8:D5:0B:C4:80:C7
- Attempting to connect to B8:D5:0B:C4:80:C7
- [CHG] Device B8:D5:0B:C4:80:C7 Connected: yes
- Connection successful

- 2.) terminal
- >bluealsa

- 3.) terminal
- gst-launch-1.0 audiotestsrc volume=0.001 ! alsasink device=bluealsa:HCI=hci0,DEV=B8:D5:0B:C4:80:C7,PROFILE=a2dp
