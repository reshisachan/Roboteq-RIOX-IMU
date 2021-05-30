# Roboteq-RIOX-IMU
Read and write data to Roboteq RIOX module
configure serial COM port in ubuntu linux 20.04
try acessing the COM port
If you ge error: Cannot open /dev/ttyS0: Permission denied
The tty devices belong to the "dialout" group,you are not a member of this group and hence are denied access to /dev/ttyS0, so you need to add yourself to that group.

First check if you are a member of that group:   groups ${USER}
..this will list all the groups you belong to. If you don't belong to the dialout group then add yourself to it, for example: sudo gpasswd --add ${USER} dialout
You then need to log out and log back in again for it to be effective. Then see if it fixes your problem.

Unfortunately serial ports are non-PlugNPlay, so kernel doesn't know which device was plugged in. After reading a HowTo tutorial I've got the working idea.

The /dev/ directory of unix like OSes contains files named as ttySn (with n being a number). Most of them doesn't correspond to existing devices. To find which ones do, issue a command:

$ dmesg | grep ttyS
[    0.872181] 00:06: ttyS0 at I/O 0x3f8 (irq = 4) is a 16550A
[    0.892626] 00:07: ttyS1 at I/O 0x2f8 (irq = 3) is a 16550A
[    0.915797] 0000:01:01.0: ttyS4 at I/O 0x9800 (irq = 19) is a ST16650V2
[    0.936942] 0000:01:01.1: ttyS5 at I/O 0x9c00 (irq = 18) is a ST16650V2
Above is an example output of my PC. You can see the initialization of a few serial ports:

ttyS0, ttyS1, ttyS4, ttyS5.

One of them is going to have a positive voltage upon a device plugged in. So by comparing the content of the file /proc/tty/driver/serial with and without the device plugged in we can easily find the ttyS related to our device. So, now do:

$ sudo cat /proc/tty/driver/serial> /tmp/1
(un)plug a device

$ sudo cat /proc/tty/driver/serial> /tmp/2
Next check the difference between the two files. Below is an output of my PC:

$ diff /tmp/1 /tmp/2
2c2
< 0: uart:16550A port:000003F8 irq:4 tx:6 rx:0
---
> 0: uart:16550A port:000003F8 irq:4 tx:6 rx:0 CTS|DSR
By comparing the three numbers with the dmesg output we can determine which one is the port:

[    0.872181] 00:06: ttyS0 at I/O 0x3f8 (irq = 4) is a 16550A
Hence, our device is /dev/ttyS0, mission accomplished!

