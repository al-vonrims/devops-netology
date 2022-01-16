# devops-netology

## Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3

#### 1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
telnet route-views.routeviews.org
Username: rviews
show ip route x.x.x.x/32
show bgp x.x.x.x/32
```

`show ip route 95.25.200.48`:
```
route-views>show ip route 95.25.200.48
Routing entry for 95.24.0.0/15
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 4w2d ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 4w2d ago
      Route metric is 0, traffic share count is 1
      AS Hops 3
      Route tag 6939
      MPLS label: none
```

`show bgp 95.25.200.48`:
```
route-views>show bgp 95.25.200.48
BGP routing table entry for 95.24.0.0/15, version 1470565969
Paths: (23 available, best #13, table default)
  Not advertised to any peer
  Refresh Epoch 1
  3333 6762 8402 8402
    193.0.0.56 from 193.0.0.56 (193.0.0.56)
      Origin IGP, localpref 100, valid, external
      Community: 6762:1 6762:92 6762:14900
      path 7FE17A390F78 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  4901 6079 3356 8402 8402
    162.250.137.254 from 162.250.137.254 (162.250.137.254)
      Origin IGP, localpref 100, valid, external
      Community: 65000:10100 65000:10300 65000:10400
      path 7FE152FC4AA8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  57866 3356 8402 8402
    37.139.139.17 from 37.139.139.17 (37.139.139.17)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:903 3356:2065 8402:900 8402:904
      path 7FE029FAF0B8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  53767 14315 6453 6453 3356 8402 8402
    162.251.163.2 from 162.251.163.2 (162.251.162.3)
      Origin IGP, localpref 100, valid, external
      Community: 14315:5000 53767:5000
      path 7FE0BDFC1BC8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  1351 6939 3216 8402
    132.198.255.253 from 132.198.255.253 (132.198.255.253)
      Origin IGP, localpref 100, valid, external
      path 7FE1093470C8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20912 3257 3356 8402 8402
    212.66.96.126 from 212.66.96.126 (212.66.96.126)
      Origin IGP, localpref 100, valid, external
      Community: 3257:8070 3257:30515 3257:50001 3257:53900 3257:53902 20912:65004
      path 7FE0EC4D2DA8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 2
  8283 6762 8402 8402
    94.142.247.3 from 94.142.247.3 (94.142.247.3)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 6762:1 6762:92 6762:14900 8283:1 8283:101
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x18
        value 0000 205B 0000 0000 0000 0001 0000 205B
              0000 0005 0000 0001
      path 7FE0A1C8B6A0 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3549 3356 8402 8402
    208.51.134.254 from 208.51.134.254 (67.16.168.191)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:903 3356:2065 3549:2581 3549:30840 8402:900 8402:904
      path 7FE0251CAC10 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3356 8402 8402
    4.68.4.46 from 4.68.4.46 (4.69.184.201)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:903 3356:2065 8402:900 8402:904
      path 7FE023B58F48 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  852 3356 8402 8402
    154.11.12.212 from 154.11.12.212 (96.1.209.43)
      Origin IGP, metric 0, localpref 100, valid, external
      path 7FE0B047E2A8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  101 3356 8402 8402
    209.124.176.223 from 209.124.176.223 (209.124.176.223)
      Origin IGP, localpref 100, valid, external
      Community: 101:20100 101:20110 101:22100 3356:2 3356:22 3356:100 3356:123 3356:501 3356:903 3356:2065 8402:900 8402:904
      Extended Community: RT:101:22100
      path 7FE0BAAC1468 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20130 6939 3216 8402
    140.192.8.16 from 140.192.8.16 (140.192.8.16)
      Origin IGP, localpref 100, valid, external
      path 7FE179232738 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  6939 3216 8402
    64.71.137.241 from 64.71.137.241 (216.218.252.164)
      Origin IGP, localpref 100, valid, external, best
      path 7FE133600990 RPKI State not found
      rx pathid: 0, tx pathid: 0x0
  Refresh Epoch 1
  2497 3216 8402
    202.232.0.2 from 202.232.0.2 (58.138.96.254)
      Origin IGP, localpref 100, valid, external
      path 7FE0F24432D8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 3
  3303 6762 8402 8402
    217.192.89.50 from 217.192.89.50 (138.187.128.158)
      Origin IGP, localpref 100, valid, external
      Community: 3303:1004 3303:1006 3303:3056 6762:1 6762:92 6762:14900
      path 7FE168003A88 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  7018 3356 8402 8402
    12.0.1.63 from 12.0.1.63 (12.0.1.63)
      Origin IGP, localpref 100, valid, external
      Community: 7018:5000 7018:37232
      path 7FE0E86BC9B0 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  7660 2516 6762 8402 8402
    203.181.248.168 from 203.181.248.168 (203.181.248.168)
      Origin IGP, localpref 100, valid, external
      Community: 2516:1030 7660:9003
      path 7FE1485208A8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  49788 12552 3216 8402
    91.218.184.60 from 91.218.184.60 (91.218.184.60)
      Origin IGP, localpref 100, valid, external
      Community: 12552:12000 12552:12100 12552:12101 12552:22000
      Extended Community: 0x43:100:1
      path 7FE1156554F8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  1221 4637 6762 8402 8402
    203.62.252.83 from 203.62.252.83 (203.62.252.83)
      Origin IGP, localpref 100, valid, external
      path 7FE02EF33350 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  701 1273 8402 8402 8402
    137.39.3.55 from 137.39.3.55 (137.39.3.55)
      Origin IGP, localpref 100, valid, external
      path 7FE0517C9228 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3257 3356 8402 8402
    89.149.178.10 from 89.149.178.10 (213.200.83.26)
      Origin IGP, metric 10, localpref 100, valid, external
      Community: 3257:8794 3257:30043 3257:50001 3257:54900 3257:54901
      path 7FE0E9814A38 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3561 3910 3356 8402 8402
    206.24.210.80 from 206.24.210.80 (206.24.210.80)
      Origin IGP, localpref 100, valid, external
      path 7FE13F205CA0 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  19214 3257 3356 8402 8402
    208.74.64.40 from 208.74.64.40 (208.74.64.40)
      Origin IGP, localpref 100, valid, external
      Community: 3257:8108 3257:30048 3257:50002 3257:51200 3257:51203
      path 7FE1435AD920 RPKI State not found
      rx pathid: 0, tx pathid: 0

```

#### 2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
`modprobe -v dummy numdummies=2`

```bash
root@vagrant:~# ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
3: dummy0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 9a:75:07:09:42:fa brd ff:ff:ff:ff:ff:ff
4: dummy1: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether d2:ef:ee:2d:9c:7d brd ff:ff:ff:ff:ff:ff
```

`nano /etc/network/interfaces`:
```
auto dummy0
iface dummy0 inet static
  address 192.168.2.5/32
  pre-up ip link add dummy0 type dummy
  post-down ip link del dummy0
```

```bash
root@vagrant:~# ifup dummy0
root@vagrant:~# ip route add 8.8.8.8/32 via 192.168.2.5
root@vagrant:~# ip route add 8.8.4.4/32 via 192.168.2.5
root@vagrant:~# ip route show
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
8.8.4.4 via 192.168.2.5 dev dummy0
8.8.8.8 via 192.168.2.5 dev dummy0
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
```

#### 3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
```bash
root@vagrant:~# ss -tpl
State             Recv-Q            Send-Q                       Local Address:Port                           Peer Address:Port            Process
LISTEN            0                 4096                               0.0.0.0:sunrpc                              0.0.0.0:*                users:(("rpcbind",pid=581,fd=4),("systemd",pid=1,fd=50))
LISTEN            0                 4096                         127.0.0.53%lo:domain                              0.0.0.0:*                users:(("systemd-resolve",pid=5682,fd=13))
LISTEN            0                 128                                0.0.0.0:ssh                                 0.0.0.0:*                users:(("sshd",pid=659,fd=3))
LISTEN            0                 4096                             127.0.0.1:8125                                0.0.0.0:*                users:(("netdata",pid=640,fd=27))
LISTEN            0                 4096                               0.0.0.0:19999                               0.0.0.0:*                users:(("netdata",pid=640,fd=4))
LISTEN            0                 4096                                     *:9100                                      *:*                users:(("node_exporter",pid=598,fd=3))
LISTEN            0                 4096                                  [::]:sunrpc                                 [::]:*                users:(("rpcbind",pid=581,fd=6),("systemd",pid=1,fd=53))
LISTEN            0                 128                                   [::]:ssh                                    [::]:*                users:(("sshd",pid=659,fd=4))
LISTEN            0                 4096                                 [::1]:8125                                   [::]:*                users:(("netdata",pid=640,fd=26))

```

#### 4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```bash
root@vagrant:~# ss -upl
State             Recv-Q            Send-Q                        Local Address:Port                          Peer Address:Port            Process
UNCONN            0                 0                             127.0.0.53%lo:domain                             0.0.0.0:*                users:(("systemd-resolve",pid=5682,fd=12))
UNCONN            0                 0                            10.0.2.15%eth0:bootpc                             0.0.0.0:*                users:(("systemd-network",pid=385,fd=19))
UNCONN            0                 0                                   0.0.0.0:sunrpc                             0.0.0.0:*                users:(("rpcbind",pid=581,fd=5),("systemd",pid=1,fd=51))
UNCONN            0                 0                                 127.0.0.1:8125                               0.0.0.0:*                users:(("netdata",pid=640,fd=25))
UNCONN            0                 0                                      [::]:sunrpc                                [::]:*                users:(("rpcbind",pid=581,fd=7),("systemd",pid=1,fd=54))
UNCONN            0                 0                                     [::1]:8125                                  [::]:*                users:(("netdata",pid=640,fd=24))
```

#### 5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.
![NetMap](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/03-sysadmin-08-net/img/NetMap.JPG)