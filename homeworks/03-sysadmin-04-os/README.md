# devops-netology

## Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

#### 1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

* поместите его в автозагрузку,
* предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
* удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
  
Выполнено.

unit-файл:
```
[Unit]
Description=Node Exporter

[Service]
User=node_exporter
EnvironmentFile=/etc/default/node_exporter
ExecStart=/usr/local/bin/node_exporter $OPTIONS
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
Процесс запускается со следующими опциями из файла `/etc/default/node_exporter`:  
`OPTIONS="--collector.textfile.directory /var/lib/node_exporter/textfile_collector"`

Добавлен в автозагрузку командой `systemctl enable node_exporter`

#### 2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
CPU:

    node_cpu_seconds_total{cpu="0",mode="system"}
    node_cpu_seconds_total{cpu="0",mode="user"}
    node_cpu_seconds_total{cpu="0",mode="iowait"}
    node_cpu_seconds_total{cpu="1",mode="system"}
    node_cpu_seconds_total{cpu="1",mode="user"}
    node_cpu_seconds_total{cpu="1",mode="iowait"}

Memory:

    node_memory_MemTotal_bytes
    node_memory_MemFree_bytes
    node_memory_MemAvailable_bytes
    node_memory_SwapTotal_bytes
    node_memory_SwapFree_bytes

Disk:

    node_disk_write_time_seconds_total{device="sda"}
    node_disk_read_time_seconds_total{device="sda"}
    node_disk_reads_completed_total{device="sda"}
    node_disk_writes_completed_total{device="sda"}
    node_disk_io_now{device="sda"} 0
    node_disk_io_time_seconds_total{device="sda"}

Network:

    node_network_receive_bytes_total{device="eth0"}
    node_network_transmit_bytes_total{device="eth0"}
    node_network_receive_drop_total{device="eth0"}
    node_network_transmit_drop_total{device="eth0"}
    node_network_receive_errs_total{device="eth0"}
    node_network_transmit_errs_total{device="eth0"}

#### 3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
* в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
* добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:

```bash
config.vm.network "forwarded_port", guest: 19999, host: 19999
```
После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.

Выполнено:

![Netdata](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/03-sysadmin-04-os/img/netdata.JPG)

#### 4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
Можно, в выводе будут следующие строки:

    [Wed Jan 12 11:00:18 2022] CPU MTRRs all blank - virtualized system.
    [Wed Jan 12 11:00:18 2022] Booting paravirtualized kernel on KVM
    [Wed Jan 12 11:00:22 2022] systemd[1]: Detected virtualization oracle.

#### 5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?
По-умолчанию имеет значение `1048576`:

```bash
root@vagrant:~# sysctl -a | grep fs.nr_open
fs.nr_open = 1048576
```
`fs.nr_open` - максимальное количество файловых дескрипторов, которые может использовать процесс.
Ограничение `ulimit -n` не позволит достичь этого числа.

#### 6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.

Выполнено:

```
root@vagrant:/# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   8076   528 pts/1    S+   12:54   0:00 sleep 1h
root           2  0.0  0.4   9836  4260 pts/0    S    12:55   0:00 -bash
root          11  0.0  0.3  11492  3436 pts/0    R+   12:55   0:00 ps aux
```

#### 7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
Конструкция `:(){ :|:& };:` - fork bomb.

`cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-1.scope`
Сработало ограничение на максимальное количество активных PID: 

```bash
root@vagrant:~# cat /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.max
2356
```
Для изменения числа процессов, которое можно создать в сессии, можно выполнить  
`ulimit -u 40` или добавить постоянный лимит `nproc` в файл `/etc/security/limits.conf`:

    #<domain>      <type>  <item>         <value>
    *              hard    nproc          40
