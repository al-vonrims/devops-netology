 devops-netology

## Домашнее задание к занятию "3.5. Файловые системы"

#### 1. Узнайте о [sparse](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных) файлах.

Разреженный файл (англ. sparse file) — файл, в котором последовательности нулевых байтов заменены на информацию об этих последовательностях (список дыр).

#### 2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
Нет. Так как это один и тот же объект. Разрешения на ссылку изменяться при изменении разрешений файла и наоборот.  


#### 3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

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

Выполнено.

#### 4. Используя `fdisk`, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
Выполнено через команду `fdisk /dev/sdb`.

#### 5. Используя `sfdisk`, перенесите данную таблицу разделов на второй диск.
Перенос таблицы разделов выполнен командой `sfdisk -d /dev/sdb | sfdisk /dev/sdc`:

#### 6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.
RAID1 собран:  
`mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1`

#### 7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.
RAID0 собран:  
`mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2`

#### 8. Создайте 2 независимых PV на получившихся md-устройствах.
Выполнено:  
`pvcreate /dev/md0`  
`pvcreate /dev/md1` 

#### 9. Создайте общую volume-group на этих двух PV.
Выполнено:
`vgcreate vg01 /dev/md0 /dev/md1`


#### 10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0. 
LV создан командой: `lvcreate -L 100 -n lv01 vg01 /dev/md1`

#### 11. Создайте `mkfs.ext4` ФС на получившемся LV
Выполнено:
```bash
root@vagrant:~# mkfs.ext4 /dev/vg01/lv01
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes
````

#### 12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.
Для монтирования раздела выполнено:  
`mkdir /tmp/new`  
`mount /dev/vg01/lv01 /tmp/new`

#### 13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.
Выполнено: `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.

#### 14.  Прикрепите вывод `lsblk`.

```
root@vagrant:~# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                    8:0    0   64G  0 disk
├─sda1                 8:1    0  512M  0 part  /boot/efi
├─sda2                 8:2    0    1K  0 part
└─sda5                 8:5    0 63.5G  0 part
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdb2                 8:18   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
    └─vg01-lv01      253:2    0  100M  0 lvm   /tmp/new
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdc2                 8:34   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
    └─vg01-lv01      253:2    0  100M  0 lvm   /tmp/new

```

#### 15. Протестируйте целостность файла:

```bash
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```

Выполнено. В выводе `0`.

#### 16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
Выполнено командой `pvmove /dev/md1 /dev/md0`.


#### 17. Сделайте `--fail` на устройство в вашем RAID1 md.
Выполнено:
```bash
root@vagrant:~# mdadm --fail /dev/md0 /dev/sdc1
mdadm: set /dev/sdc1 faulty in /dev/md0
```

#### 18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.

```bash
root@vagrant:~# dmesg | tail -n2
[14034.724175] md/raid1:md0: Disk failure on sdc1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
```

#### 19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:

```bash
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
Выполнено. В выводе `0`.

#### 20. Погасите тестовый хост, `vagrant destroy`.
Выполнено.
