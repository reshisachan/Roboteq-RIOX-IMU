# Roboteq-RIOX-IMU
Read and write data to Roboteq RIOX module
configure serial COM port in ubuntu linux 20.04
try acessing the COM port
If you ge error: Cannot open /dev/ttyS0: Permission denied
The tty devices belong to the "dialout" group,you are not a member of this group and hence are denied access to /dev/ttyS0, so you need to add yourself to that group.

First check if you are a member of that group:   groups ${USER}
..this will list all the groups you belong to. If you don't belong to the dialout group then add yourself to it, for example: sudo gpasswd --add ${USER} dialout
You then need to log out and log back in again for it to be effective. Then see if it fixes your problem.

