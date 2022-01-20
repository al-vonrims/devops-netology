# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Значение не будет присвоено (NameError: name 'c' is not defined). TypeError: unsupported operand type(s) for +: 'int' and 'str'  |
| Как получить для переменной `c` значение 12?  | с = str(a) + b  |
| Как получить для переменной `c` значение 3?  | c = a + int(b)  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

git_path = "/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops/devops-netology/"
bash_command = ["cd " + git_path, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.splitlines():
    if result.find('modified') != -1:
        prepare_result = result.replace('modified:', '')
        print(git_path + "/" + prepare_result.strip())
```

### Вывод скрипта при запуске при тестировании:
```
/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops/devops-netology/README.md
/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops/devops-netology/homeworks/02-git-04-tools/README.md

```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import sys


git_path = "/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops/devops-netology"
if len(sys.argv) == 2:
    if os.path.exists(sys.argv[1]) and os.path.isdir(sys.argv[1]):
        git_path = sys.argv[1]
    else:
        print(sys.argv[1] + " directory not found")
        exit(1)
elif len(sys.argv) > 2:
    print("To Many arguments")
    exit(1)

bash_command = ["cd " + git_path, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.splitlines():
    if result.find('modified') != -1:
        prepare_result = result.replace('modified:', '')
        print(git_path + "/" + prepare_result.strip())

```

### Вывод скрипта при запуске при тестировании:
```
aleks@trpc:/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops$ python3 task3.py
/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops/devops-netology/README.md
/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops/devops-netology/homeworks/02-git-04-tools/README.md
aleks@trpc:/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops$ python3 task3.py terraform-main
terraform-main/LICENSE
terraform-main/README.md
aleks@trpc:/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops$ python3 task3.py devops-netology
devops-netology/README.md
devops-netology/homeworks/02-git-04-tools/README.md
aleks@trpc:/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops$ python3 test2.py test
test directory not found
aleks@trpc:/mnt/c/Users/Aleksandr/PycharmProjects/netology.devops$ python3 test2.py test test2
To Many arguments
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import socket
import json

file = "ip.json"
if not os.path.exists(file):
    host_names = ["drive.google.com", "mail.google.com", "google.com"]
    json_data = dict()
    json_data["hosts"] = []
    for host in host_names:
        json_data["hosts"].append({"hostname": host, "ip": socket.gethostbyname(host)})
    with open(file, "w") as f:
        json.dump(json_data, f)

with open(file, "r") as f:
    json_data = json.load(f)

need_write = False
for host in json_data["hosts"]:
    new_ip = socket.gethostbyname(host['hostname'])
    print(f"{host['hostname']} - {new_ip}")
    if new_ip != host["ip"]:
        need_write = True
        print(f"[ERROR] {host['hostname']} IP mismatch: {host['ip']} {new_ip}")
        host['ip'] = new_ip

if need_write:
    with open(file, "w") as f:
        json.dump(json_data, f)
```

### Вывод скрипта при запуске при тестировании:
```
aleks@trpc:~$ python3 task4.py
drive.google.com - 142.251.1.194
mail.google.com - 173.194.73.83
[ERROR] mail.google.com IP mismatch: 173.194.73.19 173.194.73.83
google.com - 64.233.164.100
[ERROR] google.com IP mismatch: 64.233.164.113 64.233.164.100
aleks@trpc:~$ python3 task4.py
drive.google.com - 142.251.1.194
mail.google.com - 173.194.73.19
[ERROR] mail.google.com IP mismatch: 173.194.73.83 173.194.73.19
google.com - 64.233.164.102
[ERROR] google.com IP mismatch: 64.233.164.100 64.233.164.102
aleks@trpc:~$ python3 task4.py
drive.google.com - 142.251.1.194
mail.google.com - 173.194.73.83
[ERROR] mail.google.com IP mismatch: 173.194.73.19 173.194.73.83
google.com - 64.233.164.101
[ERROR] google.com IP mismatch: 64.233.164.102 64.233.164.101

```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```