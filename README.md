# Домашнее задание к занятию "`Резервное копирование баз данных`" - `Дедюрин Денис`

---

## Задание 1. Резервное копирование
Кейс
Финансовая компания решила увеличить надёжность работы баз данных и их резервного копирования.

Необходимо описать, какие варианты резервного копирования подходят в случаях:

1.1. Необходимо восстанавливать данные в полном объёме за предыдущий день.

1.2. Необходимо восстанавливать данные за час до предполагаемой поломки.

1.3.* Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую или починенную базу данных.

Приведите ответ в свободной форме.

### Ответ:

1.1. Для восстановления данных в полном объёме за предыдущий день подходит стратегия полного резервного копирования. Полное резервное копирование (full backup) создает копию всех данных базы данных. Это резервное копирование может выполняться ежедневно в ночное время, чтобы гарантировать наличие актуальной копии данных за предыдущий день.

1.2. Для восстановления данных за час до предполагаемой поломки подходит стратегия инкрементного или дифференциального резервного копирования в сочетании с журналами транзакций (transaction logs).

Инкрементное резервное копирование (incremental backup): копируются только те данные, которые изменились с момента последнего резервного копирования. Это позволяет быстрее создавать резервные копии и уменьшать объем данных, которые нужно хранить.

Дифференциальное резервное копирование (differential backup): копируются все изменения, произошедшие с момента последнего полного резервного копирования. Это также ускоряет процесс создания резервных копий и уменьшает объем данных для хранения.

Использование журналов транзакций (transaction logs) позволяет восстановить базу данных до любого момента времени, вплоть до последнего сохраненного журнала транзакций.

1.3. Да, такой кейс возможен с использованием технологий репликации и кластеризации баз данных. Это называется фейловер (failover).

Репликация: база данных копируется на несколько серверов в реальном времени. В случае отказа основного сервера, запросы перенаправляются на резервный сервер. Это обеспечивает непрерывность работы без потери данных.

Кластеризация: объединение нескольких серверов в кластер для обеспечения высокой доступности и отказоустойчивости. В случае сбоя одного сервера, другой сервер в кластере берет на себя его задачи.

---
## Задание 2. PostgreSQL

2.1. С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).

2.1.* Возможно ли автоматизировать этот процесс? Если да, то как?

Приведите ответ в свободной форме.

### Ответ:

2.1. Пример команды резервирования данных и восстановления БД (pg_dump/pg_restore)

Резервирование данных:
```
pg_dump -U [username] -h [hostname] -p [port] -F c -b -v -f [backup_file] [database_name]
```
Где:
```
-U [username]: имя пользователя базы данных
-h [hostname]: хост, на котором запущена база данных
-p [port]: порт, на котором работает база данных
-F c: формат резервного копирования (custom)
-b: включить бины (large objects)
-v: подробный вывод
-f [backup_file]: файл для сохранения резервной копии
[database_name]: имя базы данных
```

Восстановление данных:
```
pg_restore -U [username] -h [hostname] -p [port] -d [database_name] -v [backup_file]
```
Где:
```
-d [database_name]: имя базы данных, в которую будет выполнено восстановление
```

2.1.* Да, процесс резервного копирования и восстановления можно автоматизировать с помощью скриптов и планировщиков задач, таких как cron в Unix/Linux системах.

Пример автоматизации с использованием cron.

Создаем скрипт резервного копирования (backup.sh):
```
#!/bin/bash
PGPASSWORD='your_password'
export PGPASSWORD
pg_dump -U username -h hostname -p port -F c -b -v -f /path/to/backup_file database_name
unset PGPASSWORD
```
Добавляем задание в cron:
```
crontab -e
```
Добавляем строку для ежедневного резервного копирования в 2:00 ночи:
```
0 2 * * * /path/to/backup.sh
```

---
## Задание 3. MySQL

3.1. С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySQL.

3.1.* В каких случаях использование реплики будет давать преимущество по сравнению с обычным резервным копированием?

Приведите ответ в свободной форме.

### Ответ:

3.1. Пример команды инкрементного резервного копирования базы данных MySQL
Для инкрементного резервного копирования MySQL используется утилита mysqlbackup из MySQL Enterprise Backup.

Резервирование данных
```
mysqlbackup --incremental --incremental-base=dir:/path/to/last_full_backup --backup-dir=/path/to/incremental_backup_dir backup
```
Где:
```
--incremental: указывает на инкрементное резервное копирование
--incremental-base=dir:/path/to/last_full_backup: указывает на базовое полное резервное копирование
--backup-dir=/path/to/incremental_backup_dir: указывает на директорию для сохранения инкрементного резервного копирования
```

3.1.* В каких случаях использование реплики будет давать преимущество по сравнению с обычным резервным копированием?
Использование реплики дает следующие преимущества по сравнению с обычным резервным копированием:

Высокая доступность: реплики могут использоваться для обеспечения непрерывной работы базы данных. В случае отказа основного сервера, реплика может быть быстро переведена в состояние основного сервера (фейловер).
Балансировка нагрузки: запросы на чтение могут быть распределены между основной базой данных и репликами, что снижает нагрузку на основной сервер.
Быстрое восстановление: реплика может быть использована для быстрого восстановления базы данных без необходимости ждать восстановления из резервной копии.
Минимизация времени простоя: при использовании реплик время простоя может быть минимизировано, так как переключение на реплику происходит быстрее, чем восстановление из резервной копии.