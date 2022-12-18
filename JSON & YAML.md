# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"

## Задание 1

## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:

```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

### Ваш скрипт:
```
 1: { "info" : "Sample JSON output from our service\t",
 2:     "elements" :[
 3:         { "name" : "first",
 4:         "type" : "server",
 5:         "ip" : 7175 
 6:         },
 7:         { "name" : "second",
 8:         "type" : "proxy",
 9:         "ip": "71.78.22.43"
10:         }
11:     ]
12: }
```
Не хватало кавычек в строке "ip": "71.78.22.43"

---

## Задание 2

В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import time
import json
import yaml

hosts = {'drive.google.com': None, 'mail.google.com': None, 'google.com': None}
log_dir = "/home/vish/Netology/sysadm-homeworks/task4/"

while True:
  for i in hosts:
    time.sleep(3)
    ip = socket.gethostbyname(i)
    print(i+ ' - ' + ip)
    if ip != hosts[i]:
      print('[ERROR] ' + i + ' IP mismatch: ' + str(hosts[i]) + ' ' + ip)
        # json
      with open(log_dir+i+".json",'w') as jsf:
          json_data= json.dumps({i:ip})
          jsf.write(json_data)
        # yaml
      with open(log_dir+i+".yaml",'w') as ymf:
          yaml_data= yaml.dump([{i : ip}])
          ymf.write(yaml_data)
      hosts[i] = ip
```

### Вывод скрипта при запуске при тестировании:
```bash
vish@DevOps:~/Netology/sysadm-homeworks$ python3 task_4.py 
drive.google.com - 142.250.203.142
[ERROR] drive.google.com IP mismatch: None 142.250.203.142
mail.google.com - 216.58.215.101
[ERROR] mail.google.com IP mismatch: None 216.58.215.101
google.com - 216.58.215.78
[ERROR] google.com IP mismatch: None 216.58.215.78
drive.google.com - 142.250.203.142
mail.google.com - 216.58.215.101
google.com - 172.217.16.14
[ERROR] google.com IP mismatch: 216.58.215.78 172.217.16.14
drive.google.com - 142.250.203.142
mail.google.com - 216.58.215.101
google.com - 172.217.16.14
drive.google.com - 142.250.203.142
mail.google.com - 216.58.215.101
google.com - 172.217.16.14
drive.google.com - 142.250.203.142
mail.google.com - 216.58.215.101
google.com - 172.217.16.14
drive.google.com - 142.250.203.142
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{"drive.google.com": "142.250.203.142"}
{"google.com": "172.217.16.14"}
{"mail.google.com": "216.58.215.101"}
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
- drive.google.com: 142.250.203.142
- google.com: 172.217.16.14
- mail.google.com: 216.58.215.101
```

---
