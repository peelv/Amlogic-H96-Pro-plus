# Amlogic-H96-Pro-
my work on amlogic h96 pro + s912 (aliexpress) with the goal to install arch linux

**Specifications
ARMv8-A
cpu cortex-a53
gpu mali t820 mp3

uname -a
Linux localhost 3.14.29 #12 SMP PREEMPT Thu Dec 29 22:12:20 CST 2016 armv8l

1. install android sdk
https://wiki.archlinux.org/index.php/android

2. connect to device

**USB connect - currently not working

USB Male to Male cable

the device is not recognized as usb device

**IP connect - working

connect the 
adb kill-server
adb connect 192.168.0.173
 daemon not running. starting it now at tcp:5037 
 daemon started successfully 
connected to 192.168.0.173:5555
