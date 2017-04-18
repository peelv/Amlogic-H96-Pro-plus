# Amlogic-H96-Pro-plus
my work on amlogic h96 pro + s912 (aliexpress) with the goal to install arch linux


##Specifications

ARMv8-A 
board	  q9377 
cpu       cortex-a53 aarch64 rev 4
gpu       mali t820 mp3
opengl version supported 3.2

'uname -a'
Linux localhost 3.14.29 #12 SMP PREEMPT Thu Dec 29 22:12:20 CST 2016 armv8l

""install android sdk**

https://wiki.archlinux.org/index.php/android

##connect to device

USB connect - currently not working
-----------------------------------

used USB Male to Male cable to connect to pc running Manjaro Gnome 

the device is not recognized as usb device

'adb devices'
shows no device connected

monitor udev events
-------------------

'sudo udevadm monitor --environment --udev'

unplugging the device from usb then shows valuable infos:

UDEV  [328.600485] remove   /devices/pci0000:00/0000:00:1d.0/usb4/4-1/4-1.4/4-1.4:1.0 (usb)
ACTION=remove
DEVPATH=/devices/pci0000:00/0000:00:1d.0/usb4/4-1/4-1.4/4-1.4:1.0
DEVTYPE=usb_interface
ID_VENDOR_FROM_DATABASE=Amlogic, Inc.
INTERFACE=255/0/0
MODALIAS=usb:v1B8EpC003d0020dc00dsc00dp00icFFisc00ip00in00
PRODUCT=1b8e/c003/20
SEQNUM=3030
SUBSYSTEM=usb
TYPE=0/0/0
USEC_INITIALIZED=4287959

UDEV  [328.613418] remove   /devices/pci0000:00/0000:00:1d.0/usb4/4-1/4-1.4 (usb)
ACTION=remove
BUSNUM=004
DEVNAME=/dev/bus/usb/004/004
DEVNUM=004
DEVPATH=/devices/pci0000:00/0000:00:1d.0/usb4/4-1/4-1.4
DEVTYPE=usb_device
ID_BUS=usb
ID_MODEL=GX-CHIP
ID_MODEL_ENC=GX-CHIP
ID_MODEL_ID=c003
ID_REVISION=0020
ID_SERIAL=Amlogic_GX-CHIP
ID_USB_INTERFACES=:ff0000:
ID_VENDOR=Amlogic
ID_VENDOR_ENC=Amlogic
ID_VENDOR_FROM_DATABASE=Amlogic, Inc.
ID_VENDOR_ID=1b8e
MAJOR=189
MINOR=387
PRODUCT=1b8e/c003/20
SEQNUM=3031
SUBSYSTEM=usb
TYPE=0/0/0
USEC_INITIALIZED=3771686

create udev rule according to output
------------------------------------

'sudo gedit /usr/lib/udev/rules.d/51-android.rules'

SUBSYSTEM=="usb", ATTR{idVendor}=="1b8e", MODE="0666", GROUP="adbusers"
SUBSYSTEM=="usb",ATTR{idVendor}=="1b8e",ATTR{idProduct}=="c003",SYMLINK+="android_adb"
SUBSYSTEM=="usb",ATTR{idVendor}=="1b8e",ATTR{idProduct}=="c003",SYMLINK+="android_fastboot"


'sudo nano /etc/udev/rules.d/51-android.rules'

SUBSYSTEM=="usb", ATTR{idVendor}=="1b8e", ATTR{idProduct}=="c003", MODE="0666", GROUP="adbusers"


reload udev rules
-----------------

'sudo udevadm control --reload-rules'

**unfortunately it doesnt work and I couldnt find out why...**



##IP connect - working

'adb kill-server'
'adb connect 192.168.0.173
 daemon not running. starting it now at tcp:5037 
 daemon started successfully 
connected to 192.168.0.173:5555'

'adb devices'
'List of devices attached
192.168.0.173:5555	device'

backup system and apps to local hdd
-----------------------------------

'adb backup -all -apk -f factorybackup_all.ab'

on the android device you have to confirm the backup and add an optional password.

backup wiederherstellen
-----------------------

'adb restore factorybackup_all.ab'

##collecting data

cpuinfo
-------

'root@q9377:/ # cat /proc/cpuinfo'

Processor	: AArch64 Processor rev 4 (aarch64)
processor	: 0
processor	: 1
processor	: 2
processor	: 3
processor	: 4
processor	: 5
processor	: 6
processor	: 7
Features	: fp asimd evtstrm aes pmull sha1 sha2 crc32 wp half thumb fastmult vfp edsp neon vfpv3 tlsi vfpv4 idiva idivt 
CPU implementer	: 0x41
CPU architecture: 8
CPU variant	: 0x0
CPU part	: 0xd03
CPU revision	: 4

Hardware	: Amlogic
Serial		: 220a82001eb3087f8ede4b6cbf4e9279

meminfo
-------

'root@q9377:/ # cat /proc/meminfo'

MemTotal:        2878416 kB
MemFree:         1403056 kB
MemAvailable:    2375364 kB

more adb commands
-----------------

list all installed packages

'pm list packages'

apk ordner mit option -f anzeigen

'pm list packages -f'


'adb reboot bootloader'

rebooted to stock android, no accessible bootloader

screenshot from android

'adb screencap /path/filename.png'

move local data to android device

'adb push filename /path_on_device/' 

daten von android device holen

'adb pull /path/filename'
