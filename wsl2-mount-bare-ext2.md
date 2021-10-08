# How to mount bare metal Linux partitions to WSL under Windows?

The main prerequsity is to have *WSL2* which is available in Windows 10 2004 or higher.

1. Enable WSL2
Open up Powershell as administraor and run
`wsl --install`

you should see 
![image](https://user-images.githubusercontent.com/614444/136594287-9a4d3377-86ce-43c6-b54e-3178f9496726.png)
You need to restart your PC now.

2. Install your distro
After update open again PowerShell as Administrator and run the following command to list all available distros
`wsl --list --online`
You should get similar output
```
PS C:\WINDOWS\system32> wsl --list --online
The following is a list of valid distributions that can be installed.
Install using 'wsl --install -d <Distro>'.

NAME            FRIENDLY NAME
Ubuntu          Ubuntu
Debian          Debian GNU/Linux
kali-linux      Kali Linux Rolling
openSUSE-42     openSUSE Leap 42
SLES-12         SUSE Linux Enterprise Server v12
Ubuntu-16.04    Ubuntu 16.04 LTS
Ubuntu-18.04    Ubuntu 18.04 LTS
Ubuntu-20.04    Ubuntu 20.04 LTS
```

Pick your favorite distro a install it using the following command <>
`wsl --install -d <distro-name>`
in our case
`wsl --install -d Ubuntu-18.04`

You should see output like
![image](https://user-images.githubusercontent.com/614444/136595019-754eb302-60ec-4bae-9f34-abaaaebaa950.png)

It's Windows, so you need to do another restart :)

3. Update your kernel
Start again PowerShell as an Administrator and run 
`wsl --update` 

![image](https://user-images.githubusercontent.com/614444/136595316-598402f3-e413-4209-b20a-debef266be4e.png)

TBD:

* https://pureinfotech.com/install-windows-subsystem-linux-2-windows-10/
* https://superuser.com/questions/37512/how-to-read-ext4-partitions-on-windows

```
PS C:\WINDOWS\system32> wmic diskdrive list brief
Caption                           DeviceID            Model                             Partitions  Size
Samsung SSD 970 EVO 250GB         \\.\PHYSICALDRIVE3  Samsung SSD 970 EVO 250GB         4           250056737280
ST1500DM003-1CH16G                \\.\PHYSICALDRIVE2  ST1500DM003-1CH16G                2           1500299297280
External USB3.0 SCSI Disk Device  \\.\PHYSICALDRIVE5  External USB3.0 SCSI Disk Device  1           750153761280
CT240BX500SSD1                    \\.\PHYSICALDRIVE4  CT240BX500SSD1                    2           240054796800
ST1500DM003-1CH16G                \\.\PHYSICALDRIVE1  ST1500DM003-1CH16G                1           1500299297280
HGST HDN726040ALE614              \\.\PHYSICALDRIVE0  HGST HDN726040ALE614              1           4000784417280
```

```
jarda@WIN10:/mnt/c/WINDOWS/system32$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0   256G  0 disk
sdb      8:16   0 339.8M  1 disk
sdc      8:32   0   256G  0 disk /
sdd      8:48   0 223.6G  0 disk
├─sdd1   8:49   0   512M  0 part
└─sdd2   8:50   0 223.1G  0 part
```

```
PS C:\WINDOWS\system32> wsl --mount \\.\PHYSICALDRIVE4 --partition 2
The disk \\.\PHYSICALDRIVE4 was successfully mounted under the name 'PHYSICALDRIVE4p2'. The mountpoint can be found under the path pointed to by the automount setting (default: /mnt/wsl).
To unmount and detach the disk, run 'wsl --unmount \\.\PHYSICALDRIVE4'.
```


