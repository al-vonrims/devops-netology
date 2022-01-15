# devops-netology

## Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

#### 1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?

Windows - `ipconfig`

```
PS C:\Users\Aleksandr> ipconfig

Настройка протокола IP для Windows


Адаптер Ethernet Ethernet:

   DNS-суффикс подключения . . . . . :
   IPv4-адрес. . . . . . . . . . . . : 192.168.10.102
   Маска подсети . . . . . . . . . . : 255.255.255.0
   Основной шлюз. . . . . . . . . : 192.168.10.1

Адаптер Ethernet VirtualBox Host-Only Network:

   DNS-суффикс подключения . . . . . :
   Локальный IPv6-адрес канала . . . : fe80::a4b1:d1cd:59e3:4a16%14
   IPv4-адрес. . . . . . . . . . . . : 192.168.56.1
   Маска подсети . . . . . . . . . . : 255.255.255.0
   Основной шлюз. . . . . . . . . :

Адаптер Ethernet Сетевое подключение Bluetooth 2:

   Состояние среды. . . . . . . . : Среда передачи недоступна.
   DNS-суффикс подключения . . . . . :
```

Linux - `ip a`, `ip link show`

```
root@vagrant:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 48630sec preferred_lft 48630sec
    inet6 fe80::a00:27ff:fe73:60cf/64 scope link
       valid_lft forever preferred_lft forever
```

#### 2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
LLDP – протокол для обмена информацией между соседними устройствами, позволяет определить к какому порту коммутатора подключен сервер.
Команды `lldpd`, `lldpctl`

#### 3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
VLAN – виртуальное разделение коммутатора.
Команды - `vlan`, `vconfig`

`vim /etc/network/interfaces.d`:
```
auto eth0.10
iface eth0.10 inet static
    address 10.0.0.1
    netmask 255.255.255.0
    vlan-raw-device eth0
```

#### 4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
Типы агрегации интерфейсов в Linux:
* balance-rr
* active-backup
* balance-xor
* broadcast
* 802.3ad
* balance-tlb
* balance-alb

```
cat /etc/network/interfaces
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto bond0 eth0 eth1
iface bond0 inet static
        address 10.31.1.5
        netmask 255.255.255.0
        network 10.31.1.0
        gateway 10.31.1.254
        bond-slaves eth0 eth1
        bond-mode balance-alb
        bond-miimon 100
        bond-downdelay 200
        bond-updelay 200
```

`bond-slaves eth0 eth1` - Привязка двух настоящих сетевых интерфейсов (eth0 and eth1) к нему.  
`bond-mode balance-alb` - Задаёт тип бондинга.  
`bond-miimon 100` - Значение в миллисекундах. Определяет как часто будет проверяться состояние соединения на каждом из интерфейсов.  
`bond-downdelay 200` - Время ожидания в миллисекундах, прежде чем отключить slave в случае отказа соединения.  
`bond-updelay 200` - Время ожидания в миллисекундах, прежде чем включить slave после восстановления соединения.  

#### 5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
В сети с маской /29 8 IP-адресов.
В сети с маской /24 может быть 32 сети с маской /29.

Примеры /29 подсетей внутри сети 10.10.10.0/24:  
* 10.10.10.8/29
* 10.10.10.16/29
* 10.10.10.24/29

#### 6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
Допустимо взять из сети 100.64.0.0/10.  
Например: `100.73.0.0/26`

#### 7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?

Linux:

* `ip neigh show` - Просмотр таблицы
* `ip neigh del <ip> dev eth0` - Удаление ip
* `ip -s neigh flush all` - Очистка таблицы

Windows:
* `arp -a` - Просмотр таблицы
* `arp -d <ip>` - Удаление ip
* `netsh interface ip delete arpcache` - Очистка таблицы