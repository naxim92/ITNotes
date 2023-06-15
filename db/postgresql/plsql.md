#sql #psql 

- создать таблицу
	``` sql
	CREATE TABLE table
	(
		id integer PRIMERY KEY,
		descr varchar(255) NOT NULL
	);
	```
- удалить таблицу
	`DROP TABLE table`
- вставка в таблицу
	``` sql
	INSERT INTO table
	VALUES
	(1, 'we'),
	(2, '''re');
	```
- изменить таблицу
	``` plsql
	ALTER TABLE table
	ADD COLUMN fk_table2_id integer;
	ALTER TABLE table
	ADD CONSTRAINT fk_table_table2
	FOREIGN KEY(fk_table2_id) REFERENCES table2(id);
	```
- временная таблица и транзакции
	``` plsql
	ROLLBACK;
	BEGIN;
	CREATE TEMP TABLE my_fist_temp_table -- стоит использовать наиболее уникальное имя
	ON COMMIT DROP -- удаляем таблицу при завершении транзакции
	AS SELECT 1 AS id, CAST ('какие-то значения' AS TEXT) AS val;
	COMMIT;
	```
- приведение типов
	``` plsql
	SELECT CAST ('365' AS INT);
	SELECT '365'::INT;
	```
 - `WITH`
	 ``` plsql
	 WITH
		 cte_table_name AS ( -- задаем удобное нам имя таблицы
			 SELECT schemaname, tablename -- наш любой запрос
			 FROM pg_catalog.pg_tables -- к примеру, системная таблица с таблицами базы
			 ORDER BY 1,2
		 )
		table_1 (col,b) AS (SELECT 1,1) -- еще таблица
	 SELECT * FROM cte_table_name; -- указываем нашу таблицу
	 --по факту получим результат выполнения запроса в скобках
	```
- `LIKE && ILIKE`
	_`строка`_ LIKE _`шаблон`_ [ESCAPE спецсимвол]
	*LIKE* можно заменить на `~~`
	*ILIKE* можно заменить на `~~*` (регистронезависимый LIKE)
	`~` -  воспринимает regexp
	`_` - любой символ
	`%` - любая последовательность символов
	`NOT LIKE '%text%'` можно заменить на 
- массивы + `LIKE`
	`SELECT * FROM users_tst WHERE u_name LIKE ANY (ARRAY['В%', '%аа%', 'Ульяна Х.', 'Елисей%'])`