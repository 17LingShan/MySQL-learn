# MySQL 数据库与表

## 创建数据库

### 语法

数据库是存储数据的容器。一个数据库中可以包含多个表。要想创建表，必须首先创建数据库。

```mysql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] database_name
[CHARACTER SET charset_name]
[COLLATE collation_name]
[ENCRYPTION {'Y' | 'N'}]
```

- `CREATE DATABASE` 和 `CREATE SCHEMA` 的是一样的。
- `CREATE DATABASE` 后指定要创建的数据库的名字。
- `IF NOT EXISTS` 表示在指定的数据库不存在的情况下才创建。它是可选的。
- `CHARACTER SET charset_name` 指定数据库的字符集。它是可选的。默认使用数据库服务器的配置。
- `COLLATE collation_name` 指定数据库的排序规则。它是可选的。默认使用数据库服务器的配置。
- `ENCRYPTION` 指定数据库的是否加密。它是可选的。默认使用数据库服务器的配置。

MySQL 数据库命名的要求：

- 数据库的名字最长为 64 个字符。名字的长度还取决于操作系统。
- 数据库名可以由字母、数字、下划线、美元符号组成。

### 查看创建数据库的信息

```mysql
SHOW CREATE DATABASE database_name;

//output
+----------+---------------------------------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                                                 |
+----------+---------------------------------------------------------------------------------------------------------------------------------+
| newdb    | CREATE DATABASE `database_name` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+---------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

此语句输出结果可以看出：

- 我们使用 `CREATE DATABASE ``database_name``` 命令了创建了数据库。
- `DEFAULT CHARACTER SET utf8mb4` 表示默认的字符集是 `utf8mb4`。
- `COLLATE utf8mb4_0900_ai_ci` 表示默认的排序规则是 `utf8mb4_0900_ai_ci`。
- `DEFAULT ENCRYPTION='N'` 表示默认不启用加密。

## 删除数据库

注意：`DROP DATABASE` 语句将永久删除数据库和数据库中的所有表，请谨慎操作。

### 语法

```mysql
DROP {DATABASE | SCHEMA} [IF EXISTS] database_name;
```

- `DROP DATABASE` 和 `DROP SCHEMA` 是一样的。
- `DROP DATABASE` 关键字后指定要删除的数据库的名称。
- `IF EXISTS` 选项可以避免删除不存在数据库时发生的错误。它是可选的。

## 选择数据库

## 语法

在 MySQL 服务器中，可能有多个数据库。如果要进行查询等操作，首先应该先选择要进行操作的数据库。你可以使用 `USE` 语句选择或者切换数据库。

```mysql
use database_name;
```

### 登录指定数据库

```
mysql -u root -p -D testdb
```

### 查看当前数据库

```mysql
//1
SELECT DATABASE();

//2
STATUS

//3
SHOW TABLES;
```

## 创建表

表是关系数据库中数据存储的基本单位。在 MySQL 中，`CREATE TABLE`语句用来创建表。

### 语法

```mysql
CREATE TABLE [IF NOT EXISTS] table_name (
    column_name data_type [NOT NULL | NULL] [DEFAULT expr],
    column_name data_type [NOT NULL | NULL] [DEFAULT expr],
    ...,
    [table_constraints]
) [ENGINE=storage_engine];
```

说明：

- `CREATE TABLE` 语句创建一个给定名字 `table_name` 的表。

  - 表名可由字母、数字、下划线和美元符号组成，表名长度在 64 个字符以内。
  - 表名在一个数据库中是唯一的。
  - 新建的表会在当前默认的数据库中。如果还没有选择数据库，请使用 `db_name.table_name` 格式指定要新建的表所在的数据库。

- `IF NOT EXISTS` 指示只有给定的表不存在的时候才进行创建。它是可选的。

  如果你给定一个已经存在的表名，又没有使用 `IF NOT EXISTS` 子句，服务器会返回一个错误。

- `column_name data_type [NOT NULL | NULL] [DEFAULT expr] [AUTO_INCREMENT]` 定义了表中的一列。多个列使用逗号分隔。

  - `column_name` 是列的名字。列名可由字母、数字、下划线和美元符号组成，列名长度在 64 个字符以内。列名在一个表中是唯一的。
  - `data_type` 是数据类型，可以是 [CHAR](https://www.sjkjc.com/mysql/char/), [VARCHAR](https://www.sjkjc.com/mysql/varchar/), [INT](https://www.sjkjc.com/mysql/int/), [DATE](https://www.sjkjc.com/mysql/date/), [DATETIME](https://www.sjkjc.com/mysql/datetime/), [BIT](https://www.sjkjc.com/mysql/bit/), TEXT, [ENUM](https://www.sjkjc.com/mysql/enum/), JSON, BOLB 等。
  - `[NOT NULL | NULL]` 指示该列是否可以为 `NULL`。它是可选的。如果不指定该选项，则此列可以为 `NULL`。如果设置为 `NOT NULL`，则插入新行时该列必须有值。
  - `[DEFAULT expr]` 指示该列的默认值。它是可选的。如果不指定该选项，则此列的默认是 `NULL`。
  - ### `[AUTO_INCREMENT]` 指示该列是否是一个自增列。如果使用了此选项，则该列的值可有服务器自动产生和填充。该列的值从 `1` 开始，每增加一个行就会加 `1`。一个表中只能有一个自增列。

- `[table_constraints]` 位于列定义之后，它定义了表的约束。它是可选的。表的约束有[主键](https://www.sjkjc.com/mysql/primary-key/)、[外键](https://www.sjkjc.com/mysql/foreign-key/)、[CHECK](https://www.sjkjc.com/mysql/check-constraint/)、UNIQUE 等。

- `ENGINE=storage_engine` 子句指定了表使用的存储引擎。它是可选的。

  如果不指定此选项，则采用服务器默认的存储引擎。自 MySQL 5.5 版以来，服务器默认的引擎是由 MyISAM 变成了 InnoDB。

- 当表名或者字段名中含有空格或者其他特殊字字符时，请使用`` `包围起来。比如： `test1`。

### CREATE TABLE ... LIKE

此语句可以用来克隆另一个表的定义，它以另一个表的定义为基础创建一个新的空表，包含了原表中定义的列属性和索引。

```mysql
CREATE TABLE new_table LIKE original_table;
```

`CREATE TABLE ... LIKE` 语句创建的是一个空表。

### CREATE TABLE ... SELECT

你可以使用`CREATE TABLE ... SELECT` 语句从另一个表创建一个新表。该语句会以`SELECT`子句中的列创建新表，并将`SELECT` 的结果插入到新表中。

```mysql
CERATE TABLE new_table [AS] SELECT * FROM original_table;
```

`CREATE TABLE ... SELECT` 语句可以用来复制一个表，包含列属性和数据。

## 删除表

注意：`DROP TABLE` 语句将永久删除表和表中的数据，请谨慎操作。

### 语法

```mysql
DROP TABLE [IF EXISTS]
	table_name [, table_name] ...
```

- `DROP TABLE` 关键字后面是要删除的表名。如果要删除多个表，请使用逗号分隔表名。

- `IF EXISTS` 选项避免了删除不存在的表时发生的错误。它是可选的。

  当要删除的表中有不存在的表时：

  - 有 `IF EXISTS` 选项，不会对不存在的表报错。该语句会删除存在的表，并给出不存在的表的提示。
  - 没有 `IF EXISTS` 选项，该语句运行失败带有一个指示不能移除不存在的表的错误。该语句不会删除任何表。

- `DROP TABLE` 删除表的定义和表中的数据，以及表上触发器。

- 你需要具有要删除的每一个表的 `DROP` 权限。

## 修改表

通过 `ALTER TABLE` 语句，可以重命名表、重命名列、添加列、删除列、修改列的属性等。

### 语法

```mysql
ALTER TABLE table_name
	[alter_action options], ...
```

其中 `alter_action` 是一个修改动作，包括：

- `ADD` 关键字可用来添加列、索引、约束等，包括：
  - `ADD [COLUMN]`: 添加列
  - `ADD INDEX`: 添加索引
  - `ADD PRIMARY KEY`: 添加主键
  - `ADD FOREIGN KEY`: 添加外键
  - `ADD UNIQUE INDEX`: 添加唯一索引
  - `ADD CHECK`: 添加检查约束
- `DROP` 关键字可用来删除列、索引、约束等，包括：
  - `DROP [COLUMN] col_name`: 删除列
  - `ADD INDEX index_name`: 删除索引
  - `DROP PRIMARY KEY`: 删除主键
  - `DROP FOREIGN KEY fk_symbol`: 删除外键
  - `DROP CHECK symbol`: 删除检查约束
- `MODIFY` 关键字用来修改列的定义。与 `CHANGE` 关键字不同，它不能重命名列。例如: `MODIFY [COLUMN] col_name column_definition`。
- `CHANGE` 关键字用来修改列的定义。与 `MODIFY` 关键字不同，它可以重命名列。例如: `CHANGE [COLUMN] old_col_name new_col_name column_definition`。
- `RENAME` 关键字可以重命名列、索引和表。包括：
  - `RENAME COLUMN old_col_name TO new_col_name`: 重命名列。
  - `RENAME INDEX old_index_name TO new_index_name`: 重命名索引。
  - `RENAME new_tbl_name`: 重命名表。

## 重命名表

MySQL 提供了 `RENAME TABLE` 语句用以重命名表。除此之外，您还可以使用 `ALTER TABLE` 语句重命名表。

### 语法

```mysql
RENAME TABLE
	old_table_name TO new_table_name
	[, old_table_name2 TO new_table_name2];
#OR
ALTER TABLE old_table_name
RENAME TO new_table_name;
```

`RENAME TABLE` 语句和 `ALTER TABLE` 语句也可以用来重命名视图。

`RENAME TABLE` 语句和 `ALTER TABLE` 语句也存在一些不同：

- `RENAME TABLE` 语句更加简洁。
- 您可以在一个 `RENAME TABLE` 语句中同时重命名多个表。
- `RENAME TABLE` 语句不可以用来重命名临时表，而 `ALTER TABLE` 语句可以用来重命名临时表。

## 清空表

当我们需要清空一个表中的所有行时，除了使用 `DELETE * FROM table` 还可以使用 `TRUNCATE TABLE` 语句。

如果想要清空一个表， `TRUNCATE TABLE` 语句比 [`DELETE`](https://www.sjkjc.com/mysql/delete/) 语句更加有效。

### 语法

```mysql
TRUNCATE [TABLE] table_name;
```

其中 `TABLE` 关键字是可选的。也就是说， `TRUNCATE t;` 和 `TRUNCATE TABLE t;` 是等效的。

`TRUNCATE TABLE` 语句相当于先[将此表删除](https://www.sjkjc.com/mysql/table-drop/)掉，再[创建一个新表](https://www.sjkjc.com/mysql/table-create/)。`TRUNCATE TABLE` 语句需要对操作的表具有 `DROP` 权限。

### TRUNCATE 与 DELETE 不同

虽然 `TRUNCATE` 与 `DELETE` 类似，但是他们在以下几个方面存在不同：

- `TRUNCATE` 被归类为 DDL 语句，而 `DELETE` 被归类为 DML 语句。
- `TRUNCATE` 操作无法被回滚，而 `DELETE` 可以被回滚。
- `TRUNCATE` 操作删除和重建表，它的速度比 `DELETE` 快得多。
- `TRUNCATE` 操作会重置表的自增值，而 `DELETE` 不会。
- `TRUNCATE` 操作不会激活删除触发器，而 `DELETE` 会。
- `TRUNCATE` 操作不返回代表删除行的数量的值，它通常返回 `0 rows affected`。`DELETE` 返回删除的行数。
- 如果一个表被其他表的外键引用，对此表的 `TRUNCATE` 操作会失败。
