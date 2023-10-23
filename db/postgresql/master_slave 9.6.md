#psql #replication

### Master config

- Настройки на мастере
	``` postgresql.conf
	wal_level = replica # В WAL пишется информация, нужная slave для восстановления
	max_wal_senders = 2 # 2 - если есть hot_standby сервер
	wal_keep_segments = 32 # Число последних WAL сегментов, которые сервер не трогает, не перерабатывает и не удаляет
	archive_mode = on # Архивировать заполненные сегменты
	archive_command = 'cp %p /var/lib/pg-archive/%f'
	```
- Настройка пользователя на мастере
	``` pg_hba.conf
	#TYPE   DB             USER           ADDRESS        #METHOD
	host    replication    replication    10.0.0.2/32    md5
	```
- Создадим папку для архива на мастере
	``` bash
	mkdir /var/lib/pg-archive/
	chown postgres /var/lib/pg-archive/
	chmod 700 /var/lib/pg-archive/
	```
- Создадим пользователя на мастере
	``` PSQL
	CREATE ROLE replication WITH REPLICATION PASSWORD 'Hw572BbvG7g4cwq5' LOGIN;
	```

### Slave config

- Slave должен быть пустой (`var/lib/postgresql/9.6/main/*`)
- Slave сможет обслуживать запросы только на чтение
	``` postgresql.conf
	hot_standby = on
	```
- Настроить на slave параметры восстановления
	``` recovery.conf
	standby_mode = 'on' # on означает, что сервер продолжит синхронизироваться с мастером, а не остановится дойдя до актуального состояния
	primary_conninfo = 'host=10.0.0.1 port=5432 user=replication password=Hw572BbvG7g4cwq5'
	trigger_file = '/var/lib/postgresql/9.6/promote_to_master' # при появлении этого файла slave становится мастером
	restore_command = 'cp /var/lib/pg-archive/%f "%p"' # команда оболочки ОС, которая выполняется для извлечения архивного сегмента файлов WAL
	archive_cleanup_command =  'pg_archivecleanup /path/to/archive/ %r' # команда, которая запускается после успешного применения сегмента WAL
	```
- Создадим папку для архива на slave
	``` bash
	mkdir /var/lib/pg-archive/
	chown postgres /var/lib/pg-archive/
	chmod 700 /var/lib/pg-archive/
	```

## Promotion slave to master

В случае возникновения проблем с мастер-сервером, можно переключить работу приложения на слейв сервер. Но для этого необходимо указать slave-серверу, что он теперь станет ведущим и может выполнять запись, т.е. выполнить promote. В Postgresql 12 есть три способа выполнить promotion для slave-сервера:

1. Создать пустой файл с любым именем по пути, указанному в параметре `promote_trigger_file` конфигурационного файла `postgresql.conf`. При этом файл `standby.signal` будет удален.
2. Запустить команду `pg_ctl promote` (в контейнере с postgresql она уже установлена)
3. В консоли postgresql вызвать функцию `select pg_promote();`

Во втором и третьем случаях никакие файлы создавать не потребуется.

## Замечания

- По завершении процесса восстановления сервер переименует файл `recovery.conf` в `recovery.done`, видимо, если `standby_mode = 'off'`
- В нашей конфигурации на slave по ssh монтировалась папка с архивами мастера. `restore_command` забирал архивы WAL оттуда, а `archive_cleanup_command` удалял примененные WAL сегменты в этой папке slave сервера, по сути удаляя их на мастере (т.к. папка смонтирована по ssh). Таким образом, гарантировалось, что WAL архив не будет пухнуть ни на master, ни на slave.

## Мониторинг

- На мастер-сервере после настройки репликации команда `select * from pg_stat_replication;` должна возвращать не пустые значения, а команда `SELECT count(*) FROM pg_stat_replication`возвращать минимум значение `1`.

- На standby-сервере можно наблюдать за отставанием относительно мастера, для этого подойдут команды `select pg_is_in_recovery(),pg_is_wal_replay_paused(), pg_last_wal_receive_lsn(), pg_last_wal_replay_lsn(), pg_last_xact_replay_timestamp();`. Но высчитывать здесь уже будет сложнее.
- На мастере
    `psql -c "select application_name, client_addr, state, sync_priority, sync_state from pg_stat_replication;"``
	`psql -x -c "select * from pg_stat_replication;"`
- На slave
	`select * from pg_stat_wal_receiver;`