# Домашнее задание к занятию "3.5. Файловые системы"

1. Узнайте о [sparse](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных) файлах.

Разрежённый файл (англ. sparse file) — файл, в котором последовательности нулевых байтов заменены на информацию об этих последовательностях (список дыр). 

2 Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

Жесткая ссылка и файл, для которой она создавалась имеют одинаковые inode. Поэтому жесткая ссылка имеет те же права доступа, владельца и время последней модификации, что и целевой файл.

3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

    ```bash
    Vagrant.configure("2") do |config|
      config.vm.box = "bento/ubuntu-20.04"
      config.vm.provider :virtualbox do |vb|
        lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
        lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
        vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
        vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
      end
    end
    ```

    Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.
    
    <pre><font color="#4E9A06"><b>vagrant@vagrant</b></font>:<font color="#3465A4"><b>~</b></font>$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.5M  1 loop /snap/core18/2284
loop1                       7:1    0 61.9M  1 loop /snap/core20/1518
loop2                       7:2    0 61.9M  1 loop /snap/core20/1361
loop4                       7:4    0 43.6M  1 loop /snap/snapd/14978
loop5                       7:5    0 67.9M  1 loop /snap/lxd/22526
loop6                       7:6    0 55.5M  1 loop /snap/core18/2409
loop7                       7:7    0   47M  1 loop /snap/snapd/16010
loop8                       7:8    0 67.8M  1 loop /snap/lxd/22753
sda                         8:0    0   64G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk 
sdc                         8:32   0  2.5G  0 disk 
</pre>

<pre><b>Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors</b>
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


<b>Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors</b>
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

</pre>

4. Используя `fdisk`, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.

<pre><font color="#4E9A06"><b>agrant@vagrant</b></font>:<font color="#3465A4"><b>~</b></font>$ sudo fdisk /dev/sdb

<font color="#4E9A06">Welcome to fdisk (util-linux 2.34).</font>
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xe95b201e.

Command (m for help): n           
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-5242879, default 2048): 2048
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G

Created a new partition 1 of type &apos;Linux&apos; and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (4196352-5242879, default 4196352): 4196352
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879): 5242879

Created a new partition 2 of type &apos;Linux&apos; and of size 511 MiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
</pre>


<pre><font color="#4E9A06"><b>agrant@vagrant</b></font>:<font color="#3465A4"><b>~</b></font>$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.5M  1 loop /snap/core18/2284
loop1                       7:1    0 61.9M  1 loop /snap/core20/1518
loop2                       7:2    0 61.9M  1 loop /snap/core20/1361
loop4                       7:4    0 43.6M  1 loop /snap/snapd/14978
loop5                       7:5    0 67.9M  1 loop /snap/lxd/22526
loop6                       7:6    0 55.5M  1 loop /snap/core18/2409
loop7                       7:7    0   47M  1 loop /snap/snapd/16010
loop8                       7:8    0 67.8M  1 loop /snap/lxd/22753
sda                         8:0    0   64G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk 
├─sdb1                      8:17   0    2G  0 part 
└─sdb2                      8:18   0  511M  0 part 
sdc                         8:32   0  2.5G  0 disk 
</pre>

5. Используя `sfdisk`, перенесите данную таблицу разделов на второй диск.

<pre><font color="#4E9A06"><b>vagrant@vagrant</b></font>:<font color="#3465A4"><b>~</b></font>$ sudo sfdisk --dump /dev/sdb | sudo sfdisk /dev/sdc
Checking that no-one is using this disk right now ... OK

<b>Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors</b>
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

&gt;&gt;&gt; Script header accepted.
&gt;&gt;&gt; Script header accepted.
&gt;&gt;&gt; Script header accepted.
&gt;&gt;&gt; Script header accepted.
&gt;&gt;&gt; Created a new DOS disklabel with disk identifier 0xe95b201e.
/dev/sdc1: Created a new partition 1 of type &apos;Linux&apos; and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type &apos;Linux&apos; and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: dos
Disk identifier: 0xe95b201e

<b>Device</b>     <b>Boot</b> <b>  Start</b> <b>    End</b> <b>Sectors</b> <b> Size</b> <b>Id</b> <b>Type</b>
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
</pre>

6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.
udo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sd{b1,c1}
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.



7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.

<pre><font color="#4E9A06"><b>vagrant@vagrant</b></font>:<font color="#3465A4"><b>~</b></font>$ sudo mdadm --create /dev/md1 --level=0 --raid-devices=2 /dev/sd{b2,c2}
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.</pre>

8. Создайте 2 независимых PV на получившихся md-устройствах.
vagrant@vagrant:~$ sudo pvcreate /dev/md0
vagrant@vagrant:~$ sudo pvcreate /dev/md1


9. Создайте общую volume-group на этих двух PV.

vagrant@vagrant:~$ sudo vgcreate vg1 /dev/md0 /dev/md1
  Volume group "vg1" successfully created
  
  agrant@vagrant:~$ sudo pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda3
  VG Name               ubuntu-vg
  PV Size               <63.00 GiB / not usable 0   
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              16127
  Free PE               8063
  Allocated PE          8064
  PV UUID               sDUvKe-EtCc-gKuY-ZXTD-1B1d-eh9Q-XldxLf
   
  --- Physical volume ---
  PV Name               /dev/md0
  VG Name               vg1
  PV Size               <2.00 GiB / not usable 0   
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              511
  Free PE               511
  Allocated PE          0
  PV UUID               79WzZm-EYSY-yjw9-CNsf-gfG4-cJzf-03ufcD
   
  --- Physical volume ---
  PV Name               /dev/md1
  VG Name               vg1
  PV Size               1018.00 MiB / not usable 2.00 MiB
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              254
  Free PE               254
  Allocated PE          0
  PV UUID               P4s6mX-Bv2T-Sbcz-RXnR-U7zM-6ZYj-n7sA8f



10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.

vagrant@vagrant:~$ sudo lvcreate -L 100M vg1 /dev/md1
  Logical volume "lvol0" created.


11. Создайте `mkfs.ext4` ФС на получившемся LV.

agrant@vagrant:~$ sudo mkfs.ext4 /dev/vg1/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done


12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.

sudo mount /dev/vg1/lvol0 /tmp/new


13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.

agrant@vagrant:~$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-06-08 21:05:46--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23460219 (22M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz                    100%[=================================================================>]  22.37M  7.09MB/s    in 3.2s    

2022-06-08 21:05:49 (6.94 MB/s) - ‘/tmp/new/test.gz’ saved [23460219/23460219]


14. Прикрепите вывод `lsblk`.

agrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.5M  1 loop  /snap/core18/2284
loop1                       7:1    0 61.9M  1 loop  /snap/core20/1518
loop2                       7:2    0 61.9M  1 loop  /snap/core20/1361
loop4                       7:4    0 43.6M  1 loop  /snap/snapd/14978
loop5                       7:5    0 67.9M  1 loop  /snap/lxd/22526
loop6                       7:6    0 55.5M  1 loop  /snap/core18/2409
loop7                       7:7    0   47M  1 loop  /snap/snapd/16010
loop8                       7:8    0 67.8M  1 loop  /snap/lxd/22753
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new


15. Протестируйте целостность файла:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
vagrant@vagrant:~$ sudo pvmove --name lvol0 /dev/md1 /dev/md0
  /dev/md1: Moved: 12.00%
  /dev/md1: Moved: 100.00%



17. Сделайте `--fail` на устройство в вашем RAID1 md.

vagrant@vagrant:~$ sudo mdadm /dev/md0 --fail /dev/sdb1
mdadm: set /dev/sdb1 faulty in /dev/md0


18. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.

vagrant@vagrant:~$ dmesg | grep md0
[ 3405.895135] md/raid1:md0: not clean -- starting background reconstruction
[ 3405.895139] md/raid1:md0: active with 2 out of 2 mirrors
[ 3405.895237] md0: detected capacity change from 0 to 2144337920
[ 3405.895420] md: resync of RAID array md0
[ 3416.142203] md: md0: resync done.
[ 9971.120092] md/raid1:md0: Disk failure on sdb1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
vagrant@vagrant:~$ 


19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    `
    `checked

20. Погасите тестовый хост, `vagrant destroy`.

 vagrant@vagrant:~$ vagrant destroy

 ---



---
