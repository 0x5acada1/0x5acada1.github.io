---
layout: post
title: "RAIDの復元"
published : true
permalink: /ctf/cheatsheets/forensics/raid_recovery
tags: [CTF, Common, cheatsheet, forensics, raid]
---
## RAID5
### 前提条件
3つのディスクイメージファイルが与えられ、
そのうち一つのファイルサイズが他と異なり、破損しているとする。
```sh
$ ls -l
-rwxr-xr-x 1 kali kali 5242880 Apr  8 22:50 06f98d35.img
-rwxr-xr-x 1 kali kali    3790 Apr  8 22:50 0c584923.img
-rwxr-xr-x 1 kali kali 5242880 Apr  8 22:50 fef0d1cd.img
```
### ディスクデータの復元
RAID5は2つのHDDのXORをとることで、破損したデータの復元が可能。
復元にはpwntoolsを使用するのが楽。
```python:solver.py
from pwn import *

dir= './forensics_intergalactic_recovery/'
disk1 = read(dir + '06f98d35.img')
disk2 = read(dir + 'fef0d1cd.img')

disk3 = xor(disk1, disk2)
write(dir + '0c584923_rep.img', disk3)
```
```sh
$ ls -l
-rwxr-xr-x 1 kali kali 5242880 Apr  8 22:50 06f98d35.img
-rwxr-xr-x 1 kali kali    3790 Apr  8 22:50 0c584923.img
-rwxr-xr-x 1 kali kali 5242880 Aug 19 10:07 0c584923_rep.img
-rwxr-xr-x 1 kali kali 5242880 Apr  8 22:50 fef0d1cd.img
```

### RAID5の構築
各ディスクのloop deviceを作成してから、
mdadmでRAID5を構築する。
※mdadmで並べるloop deviceの順番は要注意。
順番次第では復元したファイルが破損していて読み取れない可能性があるため、別の順番で構築し直してみる。
#### loop deviceの作成
```sh
$ sudo losetup /dev/loop1 06f98d35.img                           
$ sudo losetup /dev/loop2 0c584923_rep.img
$ sudo losetup /dev/loop3 fef0d1cd.img    
$ losetup -a                              
/dev/loop1: []: (/ctf/htb/challange/forensics/Intergalactic Recovery/forensics_intergalactic_recovery/06f98d35.img)
/dev/loop2: []: (/ctf/htb/challange/forensics/Intergalactic Recovery/forensics_intergalactic_recovery/0c584923_rep.img)
/dev/loop3: []: (/ctf/htb/challange/forensics/Intergalactic Recovery/forensics_intergalactic_recovery/fef0d1cd.img)
```
#### RAIDの構築
```sh
$ sudo mdadm --create /dev/md0 --level 5 --raid-devices 3 /dev/loop3 /dev/loop2 /dev/loop1                 
mdadm: /dev/loop3 appears to be part of a raid array:                                                        
       level=raid5 devices=3 ctime=Fri Aug 19 10:50:04 2022                                                  
mdadm: /dev/loop2 appears to be part of a raid array:                                                        
       level=raid5 devices=3 ctime=Fri Aug 19 10:50:04 2022                                                  
mdadm: /dev/loop1 appears to be part of a raid array:                                                        
       level=raid5 devices=3 ctime=Fri Aug 19 10:50:04 2022                                                  
Continue creating array? y                                                                                   
mdadm: Defaulting to version 1.2 metadata                                                                    
mdadm: array /dev/md0 started.          

$ sudo mkdir /mnt/raid5
$ sudo mount /dev/md0 /mnt/raid5
$ ls -l /mnt/raid5
total 4178
-rwxrwxrwx 1 kali kali 4276775 Apr  8 01:44 imw_1337.pdf
```

## 補足
### losetup
```sh
$ tldr losetup                                                                                             
losetup                                                                                                      
Set up and control loop devices.More information: https://manned.org/losetup.                                
                                                                                                             
 - List loop devices with detailed info:                                                                     
   losetup -a                                                                                                
                                                                                                             
 - Attach a file to a given loop device:                                                                     
   sudo losetup /dev/{{loop}} /{{path/to/file}}                                                              
                                                                                                             
 - Attach a file to a new free loop device and scan the device for partitions:                               
   sudo losetup --show --partscan -f /{{path/to/file}}                                                       
                                                                                                             
 - Attach a file to a read-only loop device:                                                                 
   sudo losetup --read-only /dev/{{loop}} /{{path/to/file}}                                                  
                                                                                                             
 - Detach all loop devices:                                                                                  
   sudo losetup -D                                                                                           
                                                                                                             
 - Detach a given loop device:                                                                               
   sudo losetup -d /dev/{{loop}}            
```

### mdamd
```sh
$ tldr mdadm          
mdadm
RAID management utility.More information: https://manned.org/mdadm.

 - Create array:
   mdadm --create {{/dev/md/MyRAID}} --level {{raid_level}} --raid-devices {{number_of_disks}} {{/dev/sdXN}}

 - Stop array:
   mdadm --stop {{/dev/md0}}

 - Mark disk as failed:
   mdadm --fail {{/dev/md0}} {{/dev/sdXN}}

 - Remove disk:
   mdadm --remove {{/dev/md0}} {{/dev/sdXN}}

 - Add disk to array:
   mdadm --assemble {{/dev/md0}} {{/dev/sdXN}}

 - Show RAID info:
   mdadm --detail {{/dev/md0}}
```