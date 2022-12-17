## Задание 1

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
| Какое значение будет присвоено переменной `c` ?  | Будет выдаваться ошибка, т.к. типы переменных не совпадают (int и str)  |
| Как получить для переменной `c` значение 12? | Необходимо поменять тип переменной `a` на str (a = '1')  |
| Как получить для переменной `c` значение 3? | Необходимо поменять тип переменной `b` на int (b = 2)  |

----

## Задание 2

Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся.

Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?
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

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
repo_location = os.getcwd()
print('Working directory:', repo_location)
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
```
### Вывод скрипта при запуске при тестировании:
```python
vish@DevOps:~/Netology/sysadm-homeworks$ python3 git_script.py 
Working directory: /home/vish/Netology/sysadm-homeworks

file1.txt
git_script.py
```
Ошибки:
1. Переменная `is_change` лишняя, т.к. нигде не используется
2. Команда break прерывала обработку при первом совпадении, поэтому выводился лишь один файл - убрал
---

## Задание 3

Доработать скрипт выше так, чтобы он не только мог проверять локальный репозиторий в текущей директории, но и умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import os
import sys

work_dir = os.getcwd()
if len(sys.argv)>=2:
    work_dir = sys.argv[1]
print("Working directory:'", work_dir)
bash_command = ["cd " +work_dir, "git status 2>&1"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(work_dir+prepare_result)
    elif result.find('fatal') != -1:
        print('Current working directory is not a git repository')
```
### Вывод скрипта при запуске при тестировании:
```bash
vish@DevOps:~/Netology/sysadm-homeworks$ python3 git_script.py
Working directory:' /home/vish/Netology/sysadm-homeworks
/home/vish/Netology/sysadm-homeworksfile1.txt
/home/vish/Netology/sysadm-homeworksgit_script.py

vish@DevOps:~/Netology/sysadm-homeworks$ python3 git_script.py ~/Netology/sysadm-homeworks/
Working directory:' /home/vish/Netology/sysadm-homeworks/
/home/vish/Netology/sysadm-homeworks/file1.txt
/home/vish/Netology/sysadm-homeworks/git_script.py

vish@DevOps:~/Netology/sysadm-homeworks$ python3 git_script.py ~/
Working directory:' /home/vish/
Current working directory is not a git repository
```

---
## Задание 4

Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис.

Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков.

Мы хотим написать скрипт, который:

опрашивает веб-сервисы,
получает их IP,
выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>.
Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`,` mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import time

hosts = {'drive.google.com': None, 'mail.google.com': None, 'google.com': None}

while True:
  for i in hosts:
    time.sleep(3)
    ip = socket.gethostbyname(i)
    print(i + ' - ' + ip)
    if ip != hosts[i]:
      print('[ERROR] ' + i + ' IP mismatch: ' + str(hosts[i]) + ' ' + ip)
      hosts[i] = ip
```
### Вывод скрипта при запуске при тестировании:
```bash
vish@DevOps:~/Netology/sysadm-homeworks$ python3 task_4.py 
drive.google.com - 216.58.208.206
[ERROR] drive.google.com IP mismatch: None 216.58.208.206
mail.google.com - 172.217.16.37
[ERROR] mail.google.com IP mismatch: None 172.217.16.37
google.com - 216.58.209.14
[ERROR] google.com IP mismatch: None 216.58.209.14
drive.google.com - 216.58.208.206
mail.google.com - 172.217.16.37
google.com - 216.58.209.14
drive.google.com - 216.58.208.206
mail.google.com - 172.217.16.37
google.com - 216.58.209.14
drive.google.com - 216.58.208.206
mail.google.com - 172.217.16.37
google.com - 216.58.209.14
drive.google.com - 216.58.208.206
```
