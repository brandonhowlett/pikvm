# Fix RPi4 (4gb) official RPi POE+ hat fan failure



### 1) Enter read/write mode
[root@pikvm ~]# rw

### 2) Edit raspberrypi.conf
```
nano /etc/modules-load.d/raspberrypi.conf
```

##### Comment/remove snd-bcm2835  -->  Add i2c_dev
```
#snd-bcm2835
i2c_dev
```

##### Save and exit
`ctrl` + `x`,  `y`,  `enter`


### 3) Create file pikvm-poe-plus-fix.service
```
nano /etc/systemd/system/pikvm-poe-plus-fix.service
```

File contents:
```
[Unit]
Description=Fix RPi4 (4gb) official RPi POE+ hat fan failure

[Service]
ExecStart=/usr/local/bin/pikvm-bridge-fan-fix.sh

[Install]
WantedBy=multi-user.target
```

##### Set permissions to -rw-r--r--
```
chmod 644 /etc/systemd/system/pikvm-poe-plus-fix.service
```

### 4) Create file pikvm-bridge-fan-fix.sh
```
nano /usr/local/bin/pikvm-bridge-fan-fix.sh
```

File contents:
```
#!/bin/sh
sleep 30
i2cdetect -y 0
```

##### Save and exit
`ctrl` + `x`,  `y`,  `enter`

##### Set permissions to -rwxr-xr-x
```
chmod 755 /usr/local/bin/pikvm-bridge-fan-fix.sh
```

### 5) Reload systemd manager configuration
```
systemctl daemon-reload
```

### 6) Start the service; Wait ~30 seconds
```
systemctl start pikvm-poe-plus-fix.service
```

### 7) Check the service status
```
systemctl status pikvm-poe-plus-fix.service
```

##### The output should look something like this:
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

##### Exit systemctl
`ctrl` + `c`

### 8) Set the service to start on boot
```
systemctl enable pikvm-poe-plus-fix.service
```

### 9) Return to read only mode
`ro`

### 10) Reboot your system
`reboot`

## If successful, your KVM should work and the rpi poe+ fan will spin up once it reaches temp!
