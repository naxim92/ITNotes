#psql 

1. This lists databases:

SELECT datname FROM pg_database
WHERE datistemplate = false;

This lists tables in the current database

SELECT table_schema,table_name
FROM information_schema.tables
ORDER BY table_schema,table_name;

2. Select database

`\c mg_fbmessenger`

3. Select schema

``` psql
cdb_5ae186f3=# \dt
Отношения не найдены.
cdb_5ae186f3=# \dn
                       Список схем
                      Имя                      | Владелец
-----------------------------------------------+----------
 ootsp                                         | crm
<...................>
cdb_5ae186f3=# set search_path to ootsp;
cdb_5ae186f3=# \dt
All will be ok now!
```

4. Изменение типа столбца
``` psql
ALTER TABLE i_crm_customer
ALTER COLUMN avg_margin_summ TYPE numeric(12,2),
ALTER COLUMN average_summ TYPE numeric(12,2),
ALTER COLUMN margin_summ TYPE numeric(12,2);
```

3. Посмотреть job запросы к базе
`postgres=# select * from pg_stat_activity where client_addr != '127.0.0.1';``

4. Узнать размер всех бд на хосте:
SELECT pg_database.datname as "database_name", pg_database_size(pg_database.datname)/1024/1024 AS size_in_mb FROM pg_database ORDER by size_in_mb DESC;
Узнать размер БД, (вроде и с индексом можно)
SELECT pg_size_pretty( pg_database_size('mg_viber') );
И размеры всех схем в базе
``` psql
SELECT schema_name,
       pg_size_pretty(sum(table_size)::bigint) as size,
       ((sum(table_size) / pg_database_size(current_database())) * 100)::int as part
FROM (
  SELECT pg_catalog.pg_namespace.nspname as schema_name,
         pg_relation_size(pg_catalog.pg_class.oid) as table_size
  FROM   pg_catalog.pg_class
     JOIN pg_catalog.pg_namespace ON relnamespace = pg_catalog.pg_namespace.oid
) t
GROUP BY schema_name
ORDER BY sum(table_size) desc;
```

5. Узнать размер объектов в базе (в т.ч. индексов)
в схеме аккаунта
SELECT C.oid,C.relfilenode, nspname, relname AS "relation",
relkind, pg_size_pretty(pg_total_relation_size(C.oid)) AS "size"
FROM pg_class C
LEFT JOIN pg_namespace N ON (N.oid = C.relnamespace)
WHERE nspname NOT IN ('pg_catalog', 'information_schema') and nspname='repack'
ORDER BY pg_relation_size(C.oid) DESC;

6. Подробно про индекс
SELECT
    indexname,
    indexdef
FROM
    pg_indexes
WHERE
    indexname='i_crm_rule_execution';

7. Бэкап бд
pg_dump -U mg_whatsapp -h 127.0.0.1 -Fp mg_whatsapp > /tmp/mg_whatsapp_backup/db.dump.sql

8. Кол-во локов на определенной базе (тут для мониторинга через заббикса)
UserParameter=pgstat_cdb_007fbf62,psql -qtA -U postgres -h 127.0.0.1 -c "SELECT count from (SELECT d.datname, count(*) FROM pg_locks as l LEFT JOIN pg_database AS d ON l.database= d.oid group by d.datname) S1  where datname='cdb_007fbf62';"

9. Узнать инфу о репликации.
На местере
    psql -c "select application_name, client_addr, state, sync_priority, sync_state from pg_stat_replication;"
psql -x -c "select * from pg_stat_replication;"
На слейве
select * from pg_stat_wal_receiver;

10. Инфа по мониторингу
https://postgrespro.ru/docs/postgresql/10/monitoring-stats

11. Установить хорошую тулзу
sudo PATH="$PATH:/usr/pgsql-12/bin" pip3 install pgcli

12. Список индексов в базе (сначала подключиться к базе)
beru-# SELECT tablename, indexname, indexdef FROM pg_indexes WHERE schemaname = 'public' ORDER BY tablename, indexname;
Еще один
SELECT * FROM pg_indexes WHERE tablename = 'offer_mapping';



Видим что наш слейв начинает отставать от мастера:
root@slave:/# sudo -u postgres psql -c "SELECT now()-pg_last_xact_replay_timestamp();"


SELECT * FROM pg_ls_waldir() WHERE name = '000000010000000000000033';
https://habr.com/ru/company/postgrespro/blog/459250/


13. Как делать циклы - какойто запрос в консультант
-- DO
-- $do$
--     DECLARE
--         id_from INTEGER := 1;
--         id_to INTEGER := 10000;
--
--     BEGIN
--         FOR i IN 1..1000000000 LOOP
--                 UPDATE c_user u
--                 SET site_id = ses.site_id, site_customer_id = null
--                 FROM session ses
--                 WHERE u.id = ses.user_id AND u.id BETWEEN id_from AND id_to AND u.site_id IS NULL;
--                 COMMIT;
--
--                 id_from := id_to + 1;
--                 id_to := id_to + 10000;
--             END LOOP;
--     END
-- $do$;

14. Удалить из схемы паблик все таблы и сиквенсы. (!!!) Не тестировался мной, утащил у Кости
DO $$ DECLARE
r RECORD;
BEGIN
FOR r IN (SELECT tablename FROM pg_tables WHERE schemaname =
'public') LOOP
EXECUTE 'DROP TABLE IF EXISTS ' || quote_ident(r.tablename) || '
CASCADE';
END LOOP;
END $$;

DO $$ DECLARE
r RECORD;
BEGIN
FOR r IN (SELECT sequence_name FROM information_schema.sequences WHERE sequence_schema =
'public') LOOP
EXECUTE 'DROP SEQUENCE IF EXISTS ' || quote_ident(r.sequence_name) || '
CASCADE';
END LOOP;
END $$;
