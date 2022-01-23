# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            },
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import socket
import json
import yaml

file_json = "ip.json"
file_yaml = "ip.yaml"
if not os.path.exists(file_json) or not os.path.exists(file_yaml):
    host_names = ["drive.google.com", "mail.google.com", "google.com"]
    json_data = dict()
    json_data["hosts"] = []
    for host in host_names:
        json_data["hosts"].append({host: socket.gethostbyname(host)})
    with open(file_json, "w") as f:
        json.dump(json_data, f)
    with open(file_yaml, "w") as f:
        yaml.dump(json_data, f)

with open(file_json, "r") as f:
    json_data = json.load(f)

need_write = False
for host in json_data["hosts"]:
    for dns_name in host:
        new_ip = socket.gethostbyname(dns_name)
        host_ip = host[dns_name]
        print(f"{dns_name} - {new_ip}")
        if new_ip != host_ip:
            need_write = True
            print(f"[ERROR] {dns_name} IP mismatch: {host_ip} {new_ip}")
            host[dns_name] = new_ip

if need_write:
    with open(file_json, "w") as f:
        json.dump(json_data, f)
    with open(file_yaml, "w") as f:
        yaml.dump(json_data, f)
```

### Вывод скрипта при запуске при тестировании:
```
aleks@trpc:~$ python3 task2.py
drive.google.com - 108.177.14.194
mail.google.com - 74.125.131.17
[ERROR] mail.google.com IP mismatch: 74.125.131.18 74.125.131.17
google.com - 64.233.165.139
[ERROR] google.com IP mismatch: 64.233.165.101 64.233.165.139
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{"hosts": [{"drive.google.com": "108.177.14.194"}, {"mail.google.com": "74.125.131.17"}, {"google.com": "64.233.165.139"}]}
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
hosts:
- drive.google.com: 108.177.14.194
- mail.google.com: 74.125.131.17
- google.com: 64.233.165.139
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
???
```

### Пример работы скрипта:
???