#Fix for the RPi4 (4gb) powered by the official RPi POE+ hat

##  Enter read/write mode
[root@pikvm ~]# rw

##  Edit raspberrypi.conf
nano /etc/modules-load.d/raspberrypi.conf

##  Comment out/remove snd-bcm2835; Add i2c_dev
```
#snd-bcm2835
i2c_dev
```
##  Save and exit
ctrl-x ... y ... enter

##  Create file pikvm-poe-plus-fix.service
[root@pikvm ~]# nano /etc/systemd/system/pikvm-poe-plus-fix.service
```
[Unit]
Description=Fix for rpi4b (4gb) pikvm powered with official rpi poe plus hat. Gets both the HDMI-CSI bridge and hat>

[Service]
ExecStart=/usr/local/bin/pikvm-bridge-fan-fix.sh

[Install]
WantedBy=multi-user.target
```
##  Set permissions to -rw-r--r--
[root@pikvm ~]# chmod 644 /etc/systemd/system/pikvm-poe-plus-fix.service

##  Create file pikvm-bridge-fan-fix.sh
[root@pikvm ~]# nano /usr/local/bin/pikvm-bridge-fan-fix.sh
```
#!/bin/sh
sleep 30
i2cdetect -y 0
```
##  Save and exit
ctrl-x ... y ... enter

##  Set permissions to -rwxr-xr-x
[root@pikvm ~]# chmod 755 /usr/local/bin/pikvm-bridge-fan-fix.sh

##  Reload systemd manager configuration
[root@pikvm ~]# systemctl daemon-reload

##  Start the service; Wait ~30 seconds
[root@pikvm ~]# systemctl start pikvm-poe-plus-fix.service

##  Check the service status
[root@pikvm ~]# systemctl status pikvm-poe-plus-fix.service

##  The output should look something like this:
```
[root@pikvm ~]# systemctl status pikvm-poe-plus-fix.service
* pikvm-poe-plus-fix.service - Fix for rpi4b (4gb) pikvm powered with official rpi poe plus hat. Gets both the HDMI>
  Loaded: loaded (/etc/systemd/system/pikvm-poe-plus-fix.service; enabled; vendor preset: disabled)
  Active: inactive (dead) since Wed 2021-12-01 03:41:44 MSK; 3 days ago
 Process: 349 ExecStart=/usr/local/bin/pikvm-bridge-fan-fix.sh (code=exited, status=0/SUCCESS)
Main PID: 349 (code=exited, status=0/SUCCESS)
     CPU: 47ms

Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 00:                         -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 50: -- 51 -- -- -- -- -- -- -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab pikvm-bridge-fan-fix.sh[454]: 70: -- -- -- -- -- -- -- --
Dec 01 03:41:44 pikvm.lab sudo[450]: pam_unix(sudo:session): session closed for user root
Dec 01 03:41:44 pikvm.lab systemd[1]: pikvm-poe-plus-fix.service: Deactivated successfully.
```

##  Exit systemctl
[root@pikvm ~]# ctrl-c

##  Set the service to start on boot
[root@pikvm ~]# systemctl enable pikvm-poe-plus-fix.service

##  Return to read only mode
[root@pikvm ~]# ro

##  Reboot your system
[root@pikvm ~]# reboot

##  If successful, your KVM should work and the rpi poe+ fan will spin up once it reaches temp!
