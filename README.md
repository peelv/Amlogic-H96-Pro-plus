# Amlogic-H96-Pro-plus
my work on amlogic h96 pro + s912 (aliexpress) with the goal to install arch linux and use it as a multimedia pc or desktop replacement.


## Specifications

[Link to Aliexpress](https://www.aliexpress.com/item/3-16GB-Amlogic-S912-H96-Pro-Octa-Core-Android-6-0-2-4G-5GHz-Wifi-HD2/32758232218.html?spm=2114.01010208.3.29.9hnBMv&ws_ab_test=searchweb0_0,searchweb201602_4_10152_10065_10151_10068_10136_10137_10060_10138_10155_10062_10156_10134_10154_10056_10055_10054_10059_10099_10103_10102_10096_10148_10147_10052_10053_10142_10107_10050_10143_10051_9982_10084_10083_10080_10082_10081_10177_10110_10111_10112_10113_10114_10078_10079_10073_10070_10123_10124-9982,searchweb201603_9,afswitch_1,ppcSwitch_7&btsid=d3b855aa-265a-46fc-988c-f5d06bd93689&algo_expid=31abaca9-8b8b-4659-87a0-cf5b217e1883-3&algo_pvid=31abaca9-8b8b-4659-87a0-cf5b217e1883)

ARMv8-A 
board	  q9377 
cpu       cortex-a53 aarch64 rev 4
gpu       mali t820 mp3
opengl version supported 3.2

​
### install android sdk

As I am using Manjaro Gnome, these steps apply:

https://wiki.archlinux.org/index.php/android

### connect to device

#### USB connect - currently not working

used USB Male to Male cable to connect to pc running Manjaro Gnome 

the device is not recognized as usb device in dmesg, df -h, gparted, so no adb connect

```{r, engine='bash', count_lines}
adb devices
```

shows no device connected

#### monitor udev events

start monitoring of udev events

```{r, engine='bash', count_lines}
sudo udevadm monitor --environment --udev
```
unplugging the device from usb then shows valuable infos:

```{r, engine='bash', count_lines}
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
```

create udev rule according to output
------------------------------------

```{r, engine='bash', count_lines}
sudo nano /usr/lib/udev/rules.d/51-android.rules
```

SUBSYSTEM=="usb", ATTR{idVendor}=="1b8e", MODE="0666", GROUP="adbusers"
SUBSYSTEM=="usb",ATTR{idVendor}=="1b8e",ATTR{idProduct}=="c003",SYMLINK+="android_adb"
SUBSYSTEM=="usb",ATTR{idVendor}=="1b8e",ATTR{idProduct}=="c003",SYMLINK+="android_fastboot"

```{r, engine='bash', count_lines}
sudo nano /etc/udev/rules.d/51-android.rules
```

SUBSYSTEM=="usb", ATTR{idVendor}=="1b8e", ATTR{idProduct}=="c003", MODE="0666", GROUP="adbusers"


reload udev rules
-----------------

```{r, engine='bash', count_lines}
sudo udevadm control --reload-rules
```

**unfortunately it doesnt work, I still cannot connect to the device using adb on USB.** 


## use IP to connect via adb - working

get the android box's IP adress and use adb to connect :-)

```{r, engine='bash', count_lines}
adb kill-server
 adb connect 192.168.0.173
 daemon not running. starting it now at tcp:5037 
 daemon started successfully 
connected to 192.168.0.173:5555
```

```{r, engine='bash', count_lines}
adb devices
List of devices attached
192.168.0.173:5555	device
```

## backup system and apps to local hdd

```{r, engine='bash', count_lines}
adb backup -all -apk -f factorybackup_all.ab
```

on the android device you have to confirm the backup and add an optional password.

## collecting data

```{r, engine='bash', count_lines}
adb shell
```
you can switch to root without entering a password. 

```{r, engine='bash', count_lines}
uname -a
Linux localhost 3.14.29 #12 SMP PREEMPT Thu Dec 29 22:12:20 CST 2016 armv8l
```

### cpuinfo


```{r, engine='bash', count_lines}
root@q9377:/ # cat /proc/cpuinfo

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
```

### meminfo

```{r, engine='bash', count_lines}
root@q9377:/ # cat /proc/meminfo

MemTotal:        2878416 kB
MemFree:         1403056 kB
MemAvailable:    2375364 kB
```

## Links

Mali t820 Midgard

### Overview

https://developer.arm.com/products/graphics-and-multimedia/mali-gpus/mali-t800-series-gpu

### download linux driver

https://developer.arm.com/products/software/mali-drivers/midgard-kernel

TX011-SW-99002-r17p0-01rel0.tgz

### Mali Ressources

https://developer.arm.com/graphics/

## status of work

waiting for development work on gpu driver for linux. 

I was able to boot the H96 with an unstable beta version of LibreElec.

## more adb commands

restore backup

```{r, engine='bash', count_lines}
adb restore factorybackup_all.ab
```

list all installed packages

```{r, engine='bash', count_lines}
pm list packages
```

show apk folder with option -f 

```{r, engine='bash', count_lines}
pm list packages -f
```

```{r, engine='bash', count_lines}
adb reboot bootloader
```

result:

rebooted to stock android, no accessible bootloader

screenshot from android

```{r, engine='bash', count_lines}
adb screencap /path/filename.png
```

move local data to android device

```{r, engine='bash', count_lines}
adb push filename /path_on_device/
```

get data from android device

```{r, engine='bash', count_lines}
adb pull /path/filename
```
