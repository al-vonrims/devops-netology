# devops-netology

## Домашнее задание к занятию "5.2. Применение принципов IaaC в работе с виртуальными машинами".

#### Задача 1. Опишите своими словами основные преимущества применения на практике IaaC паттернов. Какой из принципов IaaC является основополагающим?

Основные преимущества:

- Увеличение скорости выполнения операций
- Уменьшение стоимости обслуживания инфраструктуры
- Снижение рисков человеческой ошибки

Основополагающий принцип IaaC - Идемпотентность. Неизменность результата в не зависимости от количества повторов операций.

#### Задача 2. Чем Ansible выгодно отличается от других систем управление конфигурациями? Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?

Основное преимущество Ansible - это возможность его запуска без установки дополнительного программного обеспечения. Для взаимодействия использует SSH.  
Метод PUSH более удобен и надежен. При его использовании вся информация для развертывания инфраструктуры может находиться на одном управляющем сервере. 

#### Задача 3

Установить на личный компьютер:

- VirtualBox
```bash
root@minesrv2:~# vboxmanage --version
6.1.32_Ubuntur149290
```

- Vagrant
```bash
root@minesrv2:~# vagrant --version
Vagrant 2.2.19
```

- Ansible
```bash
root@minesrv2:~# ansible --version
ansible 2.9.6
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.8.10 (default, Mar 15 2022, 12:22:08) [GCC 9.4.0]
```

*Приложить вывод команд установленных версий каждой из программ, оформленный в markdown.*