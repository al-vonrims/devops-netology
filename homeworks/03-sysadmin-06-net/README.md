# devops-netology

## Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"

#### 1. Работа c HTTP через телнет.
- Подключитесь утилитой телнет к сайту stackoverflow.com
`telnet stackoverflow.com 80`
- отправьте HTTP запрос
```bash
GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
```
- В ответе укажите полученный HTTP код, что он означает?

`HTTP/1.1 301 Moved Permanently` - "Перемещён на постоянной основе". Этот код ответа значит, что URI запрашиваемого ресурса был изменён. Возможно, новый URI будет предоставлен в ответе.


#### 2. Повторите задание 1 в браузере, используя консоль разработчика F12.
- откройте вкладку `Network`
- отправьте запрос http://stackoverflow.com
- найдите первый ответ HTTP сервера, откройте вкладку `Headers`
- укажите в ответе полученный HTTP код.
- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
- приложите скриншот консоли браузера в ответ.

HTTP код ответа: `307 Internal Redirect`.  
Дольше всего обрабатывался запрос `https://stackoverflow.com/questions`. Тип: document. Время: 235 ms.

![DevTools](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/03-sysadmin-06-net/img/DevTools.JPG)

#### 3.  Какой IP адрес у вас в интернете?

IP: 95.25.248.52  
```bash
root@vagrant:~# curl ifconfig.me/ip
95.25.248.52
```

#### 4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой `whois`

Провайдер Beeline (ПАО Вымпелком), ранее CORBINA TELECOM.  
`AS` - AS8402

`whois 95.25.248.52`
```bash
inetnum:        95.24.0.0 - 95.30.255.255
netname:        BEELINE-BROADBAND
descr:          Dynamic IP Pool for broadband customers
country:        RU
admin-c:        CORB1-RIPE
tech-c:         CORB1-RIPE
status:         ASSIGNED PA
mnt-by:         RU-CORBINA-MNT
created:        2010-05-12T10:14:50Z
last-modified:  2011-10-24T07:14:07Z
source:         RIPE

role:           CORBINA TELECOM Network Operations
address:        PAO Vimpelcom - CORBINA TELECOM/Internet Network Operations
address:        111250 Russia Moscow Krasnokazarmennaya, 12
phone:          +7 495 755 5648
fax-no:         +7 495 787 1990
remarks:        -----------------------------------------------------------
remarks:        Feel free to contact Corbina Telecom NOC to
remarks:        resolve networking problems related to Corbina
remarks:        -----------------------------------------------------------
remarks:        User support, general questions: support@corbina.net
remarks:        Routing, peering, security: ipnoc@corbina.net
remarks:        Report spam and abuse: abuse@beeline.ru
remarks:        Mail and news: postmaster@corbina.net
remarks:        DNS: hostmaster@corbina.net
remarks:        Engineering Support ES@beeline.ru
remarks:        -----------------------------------------------------------
admin-c:        SVNT1-RIPE
tech-c:         SVNT2-RIPE
nic-hdl:        CORB1-RIPE
mnt-by:         RU-CORBINA-MNT
abuse-mailbox:  abuse-b2b@beeline.ru
created:        1970-01-01T00:00:00Z
last-modified:  2021-04-12T10:52:26Z
source:         RIPE # Filtered

% Information related to '95.25.248.0/24AS8402'

route:          95.25.248.0/24
descr:          RU-CORBINA-BROADBAND-POOL1
origin:         AS8402
mnt-by:         RU-CORBINA-MNT
created:        2011-04-28T08:56:20Z
last-modified:  2011-04-28T08:56:20Z
source:         RIPE # Filtered

% This query was served by the RIPE Database Query Service version 1.102.2 (BLAARKOP)
```

#### 5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой `traceroute`
AS8402, AS15169

```bash
traceroute -An 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  192.168.10.1 [*]  1.045 ms  0.839 ms  0.744 ms
 2  78.107.1.253 [AS8402]  1.401 ms  1.590 ms  1.479 ms
 3  * * *
 4  213.234.224.143 [AS8402]  2.317 ms  2.233 ms *
 5  85.21.224.191 [AS8402]  2.753 ms *  2.577 ms
 6  * * *
 7  * * *
 8  216.239.57.222 [AS15169]  18.709 ms 108.170.250.146 [AS15169]  3.098 ms 108.170.250.33 [AS15169]  14.047 ms
 9  108.170.250.66 [AS15169]  12.390 ms 172.253.51.243 [AS15169]  18.498 ms *
10  * 142.250.239.64 [AS15169]  17.708 ms *
11  209.85.254.20 [AS15169]  16.334 ms 172.253.51.245 [AS15169]  20.473 ms *
12  172.253.51.187 [AS15169]  18.245 ms * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  8.8.8.8 [AS15169]  16.649 ms *  17.585 ms
```
#### 6. Повторите задание 5 в утилите `mtr`. На каком участке наибольшая задержка - delay?

Наибольшая задержка на участке:
```
9. 209.85.255.136 
10. 72.14.238.168  
11. 216.239.58.67 
```
```
                            My traceroute  [v0.93]
vagrant (10.0.2.15)                                  2022-01-13T19:52:42+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                     Packets               Pings
 Host                              Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. AS???    10.0.2.2               0.0%    51    0.5   1.5   0.4   8.0   1.4
 2. AS???    192.168.10.1           0.0%    50    5.0   2.4   1.1   6.8   1.3
 3. AS8402   78.107.1.253           0.0%    50    6.5   3.4   2.0   6.5   1.2
 4. (waiting for reply)
 5. AS8402   213.234.224.144       57.1%    50    4.2   3.6   2.6   7.3   1.1
 6. AS8402   85.21.93.129           0.0%    50    5.3   4.9   3.7  13.7   1.6
 7. AS15169  108.170.250.129        0.0%    50    4.4   6.2   4.0  34.1   5.6
 8. AS15169  108.170.250.130        0.0%    50    6.3   4.6   3.7   7.5   0.9
 9. AS15169  209.85.255.136        24.0%    50   21.9  19.9  18.7  24.9   1.5
10. AS15169  72.14.238.168          0.0%    50   20.4  20.2  17.1  42.1   4.8
11. AS15169  216.239.58.67          0.0%    50   20.2  20.1  19.1  22.7   0.9
12. (waiting for reply)
13. (waiting for reply)
14. (waiting for reply)
15. (waiting for reply)
16. (waiting for reply)
17. (waiting for reply)
18. (waiting for reply)
19. (waiting for reply)
20. (waiting for reply)
21. AS15169  8.8.8.8               50.0%    50   18.1  18.6  17.0  22.0   1.3
```

#### 7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой `dig`

DNS сервера:
```bash
root@vagrant:~# dig dns.google ns

; <<>> DiG 9.16.1-Ubuntu <<>> dns.google ns
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 42288
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google.                    IN      NS

;; ANSWER SECTION:
dns.google.             6678    IN      NS      ns2.zdns.google.
dns.google.             6678    IN      NS      ns1.zdns.google.
dns.google.             6678    IN      NS      ns4.zdns.google.
dns.google.             6678    IN      NS      ns3.zdns.google.

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu Jan 13 20:09:17 UTC 2022
;; MSG SIZE  rcvd: 116
```

А-записи:
```bash
root@vagrant:~# dig dns.google

; <<>> DiG 9.16.1-Ubuntu <<>> dns.google
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18355
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google.                    IN      A

;; ANSWER SECTION:
dns.google.             2518    IN      A       8.8.8.8
dns.google.             2518    IN      A       8.8.4.4

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu Jan 13 20:10:14 UTC 2022
;; MSG SIZE  rcvd: 71
```

#### 8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой `dig`
Доменное имя: `dns.google`
```bash
root@vagrant:~# dig -x 8.8.8.8

; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 42012
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;8.8.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.   5713    IN      PTR     dns.google.

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu Jan 13 20:11:43 UTC 2022
;; MSG SIZE  rcvd: 73
```
```bash
root@vagrant:~# dig -x 8.8.4.4

; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29510
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.   33361   IN      PTR     dns.google.

;; Query time: 4 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Thu Jan 13 20:13:35 UTC 2022
;; MSG SIZE  rcvd: 73

```