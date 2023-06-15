#psql #wal

-  Сопоставить LSN и имя файла WAL
	`SELECT pg_walfile_name('0/3933A034');`
-  Посмотреть файлы wal
	`SELECT * FROM pg_ls_waldir() LIMIT 1;`
	name | size | modification (datetime)

-  Как посмотреть LSN внутри страницы данных
	``` SQL
	CREATE EXTENSION pageinspect;
	SELECT lsn FROM page_header(get_raw_page('my_table', 0));
	```
- Размер wal (в байтах), соответсвующей нашей транзакции можно узнать вычитание LSN-LSN
	`SELECT 'LSN2'::pg_lsn - 'LSN1':pg_lsn;`
	Таким образом можно смотреть как много wal-логов генерится в системе (например за сутки, за час и тд)
-  Утилита pg_waldump
	Программа `pg_waldump` показывает содержимое журнала предзаписи (WAL) и прежде всего полезна для целей отладки и обучения.