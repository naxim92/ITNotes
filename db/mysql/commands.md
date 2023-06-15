#mysql #sql

- `SHOW DATABASES;`
- `CREATE DATABASE db_name;`
- `DROP DATABASE db_name;`
- `USE db_name;`
- `SHOW TABLES;`
- 
``` SQL
CREATE TABLE table_name(
id INT AUTO_INCREMENT PRIMARY KEY,
description VARCHAR(255) NOT NULL,
f_id INT NOT NULL,
FOREIGN KEY (f_id) references another_table(id)
);
```
- `SHOW COLUMNS FROM table_name;`
- 
``` SQL
INSERT INTO table_name (decription) VALUES ("descr");
```
- `SELECT id AS 'ID', DISTINCT description FROM table_name WHERE description LIKE ORDER BY id DESC LIMIT 2;`
- 
``` SQL
ALTER TABLE table_name ADD name VARCHAR(16);
```
- `UPDATE table_name SET name = ''`
- `SELECT * FROM table_name WHERE id BETWEEN 1 AND 10;`
- `DELETE FROM table_name EHERE id = 6;`
-  