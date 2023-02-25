# MySQL 基础

## SELECT

### SELECT 语法

```mysql
SELECT columns_list
FROM table_name;
```

- 关键字`SELECT`后跟着一个或多个数据表的列。
- columns \_list 可以有多个列，他们之间用逗号隔开。
- 当药检所数据表的所有列时，可以用通配符：`SELECT * FROM table_name`。
- 关键字 FROM 后跟着要从中检索数据的表名。

虽然 SQL 不区分大小写，但是将 SQL 关键字书写为大写是个好习惯。

### 没有 FROM 的 SELECT

某些情况下需要检索的数据不存在任何表中，此时可以省略`FROM`子句。

```mysql
SELECT expression_list
```

查询系统时间：

```mysql
SELECT NOW();
```

数值计算：

```mysql
SELECT 1 + 5;
```

### 虚拟表 dual

上面没有`FROM`子句的情况，可以添加一个虚拟表 dual。

```mysql
SELECT NOW() FROM dual;
SELECT 1 + 5 FROM dual;
```

dual 完全可以忽略，存在目的只是为了让你的 SQL 语句更加工整。

## WHERE

### WHERE 语法

```mysql
SELECT columns_list
FROM table_name
WHERE quiery_condition;
```

query_condition 就是查询条件，结果是一个布尔值，结果可能为`TRUE`、`FALSE或`者`UNKNOWN`。`SELECT` 语句返回的结果就是满足查询条件为`TRUE`的记录。

查询条件一般用来比较某个字段是否匹配某个值：

```
columns_name = value
```

查询条件可以使用` AND`、`OR` 和`NOT`逻辑运算符的一个或多个组合。

`WHERE`子句还可以用在` UPDATE`和`DELETE`语句中，用来指定要更新或删除的行。

### 比较运算符

| 比较运算符  | 说明                               | 举例                  |
| :---------: | ---------------------------------- | --------------------- |
|      =      | 等于                               | age = 18              |
|     <>      | 不等于                             | age <> 18             |
|     !=      | 不等于                             | age != 18             |
|      >      | 大于，通常用于比较数字或日期       | age > 18              |
|     >=      | 大于等于，通常用于比较数字或者日期 | age >= 18             |
|      <      | 小于，通常用于比较数字或者日期     | age < 18              |
|     <=      | 小于等于，通常用于比较数字或者日期 | age <= 18             |
|     IN      | 判断值是否在一个集合中             | age IN (18, 19)       |
|   NOT IN    | 判断值是否不在一个集合中           | age NOT IN (18, 19)   |
|   BETWEEN   | 判断值是否介于两个数中间           | age BETWEEN 16 AND 18 |
|    LIKE     | 模糊匹配                           | name LIKE 'A%'        |
|   IS NULL   | 是否为 NULL                        | name IS NULL          |
| IS NOT NULL | 是否不为 NULL                      | name IS NOT NULL      |

```mysql
//BETWEEN 语法
expression BETWEEN min AND max
expression NOT BETWEEN min AND max
//相当于下面写法的简写
expression >= min AND expression <= max
expression < min OR expression > max
```

```mysql
//LIKE 语法
expression LIKE pattern
//pattern 可以是一个字段名、值或者其他表达式（函数调用、运算等）
//pattern 是一个字符串模式，MySQL字符串模式支持两个通配符：% 和 _
// %匹配零或多个任意字符
// _匹配单个任意字符
// 入股哦需要匹配通配符，则需要使用 \ 转义字符，如 \% 和 \_
// 使用通配符匹配文本时，不区分字母大小写。
// 如果 expression 与 pattern 匹配，LIKE 运算符返回 1，否则返回 0
SELECT 'a' LIKE 'a', 'a' LIKE 'a%', 'ab' LIKE 'a%', 'ab' LIKE '%a';
```

## EXISTS

在 MySQL 中，`EXISTS`操作符用来判断一个子查询是否返回数据行。如果一个子查询返回了至少一个数据行，则 `EXISTS` 的计算结果为 `TRUE`，否则计算结果为 `FALSE`。

`EXISTS`操作符是一个单目运算符，他需要一个子查询作为参数：

```mysql
SELECT columns_name
FROM table_name
WHERE EXISTS(subquery);
```

- `EXISTS`一般用在`WHERE`子句。
- 如果子查询 subquery 返回了至少一个数据行，则`EXISTS`的计算结果为`TRUE`，否则为`FALSE`。
- `EXIST`运算时，一旦子查询到一个匹配的行，`EXISTS`运算就会返回。
- `EXISTS` 不关心子查询中的列的数量或者名称，它只在乎子查询是否返回数据行。所以在 `EXISTS` 的子查询中，无论你是使用 `SELECT 1` 还是 `SELECT *`，亦或是 `SELECT column_list`，都不影响 `EXISTS` 运算的结果。
- `NOT EXISTS` 则是 `EXISTS` 的否定操作。

`EXISTS`有时候可以用`IN`实现

```mysql
SELECT *
FROM language
WHERE EXISTS(
    SELECT *
    FROM film
    WHERE film.language_id = language.language_id
  );
//对应IN
SELECT *
FROM language
WHERE language_id IN (
    SELECT DISTINCT language_id
    FROM film
  );
```

大多数情况下，使用 `EXISTS` 的语句的性能比对应的使用 `IN` 的语句要好。

## ORDER BY

通常情况下，`SELECT` 语句返回的结果集是按照数据库默认的规则排序的。如果我们想按照自定义自定义规则排序结果集，可以使用 `ORDER BY` 子句。

在 `ORDER BY` 子句中，我们可以指定一个或多个排序的字段。 `ORDER BY` 子句的语法如下：

```mysql
SELECT
 column1, column2, ...
FROM
 table_name
[WHERE clause]
ORDER BY
 cloumn1 [ASC|DESC],
 cloums2 [ASC|DESC],
 ...;
```

- `[ASC|DESC]`代表排序是升序还是降序（ASC 是升序），默认是 ASC。
- 当指定多个列时，首先按照前面的字段排序，其次按照后面的字段排序。

### CASE 实现自定义排序

```mysql
SELECT
	film_id, title, rating
FROM
	film
ORDER BY CASE rating
	WHEN 'G' THEN 1
	WHEN 'PG' THEN 2
	WHEN 'PG-13' THEN 3
	WHEN 'R' THEN 4
	WHEN 'NC-17' THEN 5
END;
```

在这个例子中，我们使用 `CASE` 将电影的等级转换为一个索引数字。然后使用 `ORDER BY` 按照这个数字进行排序。

### FIELD()函数实现自定义排序

```mysql
SELECT *
FROM film
ORDER BY FILED(rating, 'G', 'PG', 'PG-13', 'R', 'NC-17');
```

# MySQL ORDER BY

在本文中，我们介绍了在 MySQL 中的如何使用 `ORDER BY` 子句排序 `SELECT` 语句的结果集。

通常情况下，`SELECT` 语句返回的结果集是按照数据库默认的规则排序的。如果我们想按照自定义自定义规则排序结果集，可以使用 `ORDER BY` 子句。

我们可以通过 `ORDER BY` 子句指定排序的字段以及升序排序还是降序排序。

## MySQL ORDER BY 语法

在 `ORDER BY` 子句中，我们可以指定一个或多个排序的字段。 `ORDER BY` 子句的语法如下：

```sql
SELECT
   column1, column2, ...
FROM
   table_name
[WHERE clause]
ORDER BY
   column1 [ASC|DESC],
   column2 [ASC|DESC],
   ...;
```

说明：

- `ORDER BY` 子句可以指定一个或多个字段。
- `[ASC|DESC]` 代表排序是升序还是降序，这是可选的。
- `ASC` 代表升序，`DESC` 代表降序。
- 未指定 `[ASC|DESC]` 时，默认值是 `ASC`。即，默认是按指定的字段升序排序。
- 当指定多个列时，首先按照前面的字段排序，其次按照后面的字段排序。

## MySQL ORDER BY 排序规则说明

- `ORDER BY column ASC;`

  此 `ORDER BY` 子句对结果集按 `column` 字段的值升序排序。

- `ORDER BY column DESC;`

  此 `ORDER BY` 子句对结果集按 `column` 字段的值降序排序。

- `ORDER BY column;`

  此 `ORDER BY` 子句对结果集按 `column` 字段的值升序排序。这个语句等效于： `ORDER BY column ASC;`。

- `ORDER BY column1, column2;`

  此 `ORDER BY` 子句对结果集先按 `column1` 字段的值升序排序，然后再按 `column2` 字段的值升序排序。

  也就是说主排序按 `column1` 字段升序排序，在主排序的基础上，对 `column1` 字段相同的行，再按 `column2` 字段升序排序。

- `ORDER BY column1 DESC, column2;`

  此 `ORDER BY` 子句对结果集先按 `column1` 字段的值降序排序，然后再按按 `column2` 字段的值升序排序。

  也就是说主排序按 `column1` 字段降序排序，在主排序的基础上，对 `column1` 字段相同的行，再按 `column2` 字段升序排序。

## MySQL ORDER BY 实例

在以下实例中，我们使用 [Sakila 示例数据库](https://www.sjkjc.com/mysql/sample-database/)中的 [`actor`](https://www.sjkjc.com/sakila/table-actor/) 表进行演示。

### 按字段升序排序

以下 SQL 语句使用 `ORDER BY` 子句按演员姓氏升序进行排序。

```sql
SELECT
    actor_id, first_name, last_name
FROM
    actor
ORDER BY last_name;
```

```txt
+----------+-------------+--------------+
| actor_id | first_name  | last_name    |
+----------+-------------+--------------+
|       92 | KIRSTEN     | AKROYD       |
|       58 | CHRISTIAN   | AKROYD       |
|      182 | DEBBIE      | AKROYD       |
|      194 | MERYL       | ALLEN        |
|      118 | CUBA        | ALLEN        |
|      145 | KIM         | ALLEN        |
...
```

### 按字段降序排序

以下 SQL 语句使用 `ORDER BY` 子句按演员姓氏降序进行排序。

```sql
SELECT
    actor_id, first_name, last_name
FROM
    actor
ORDER BY last_name DESC;
```

```fallback
+----------+-------------+--------------+
| actor_id | first_name  | last_name    |
+----------+-------------+--------------+
|      111 | CAMERON     | ZELLWEGER    |
|       85 | MINNIE      | ZELLWEGER    |
|      186 | JULIA       | ZELLWEGER    |
|       63 | CAMERON     | WRAY         |
|      156 | FAY         | WOOD         |
|       13 | UMA         | WOOD         |
|      144 | ANGELA      | WITHERSPOON  |
|       68 | RIP         | WINSLET      |
....
```

### 按多字段排序

以下 SQL 语句使用 `ORDER BY` 子句先按演员姓氏升序排序，再按演员名字升序排序。

```sql
SELECT
    actor_id, first_name, last_name
FROM
    actor
ORDER BY last_name, first_name;
```

```fallback
+----------+-------------+--------------+
| actor_id | first_name  | last_name    |
+----------+-------------+--------------+
|       58 | CHRISTIAN   | AKROYD       |
|      182 | DEBBIE      | AKROYD       |
|       92 | KIRSTEN     | AKROYD       |
|      118 | CUBA        | ALLEN        |
|      145 | KIM         | ALLEN        |
|      194 | MERYL       | ALLEN        |
....
```

## 按自定义顺序排序

有时候单纯的按照字段的值排序并不能满足要求，我们需要按照自定义的顺序的排序。比如，我们需要按照电影分级 `'G', 'PG', 'PG-13', 'R', 'NC-17'` 的顺序对影片进行排序。

对于这样的需求，它可以理解为按照列表中元素的索引位置进行排序。我们分别使用 `CASE` 子句或 `FIELD()` 函数实现它。

在以下实例中，我们使用 [Sakila 示例数据库](https://www.sjkjc.com/mysql/sample-database/)中的 [`film` 表](https://www.sjkjc.com/sakila/table-film/)作为演示。

假设您要根据影片的等级按照的 `'G', 'PG', 'PG-13', 'R', 'NC-17'` 顺序对影片进行排序。

### 使用 `CASE` 实现自定义排序

```sql
SELECT
    film_id, title, rating
FROM
    film
ORDER BY CASE rating
    WHEN 'G' THEN 1
    WHEN 'PG' THEN 2
    WHEN 'PG-13' THEN 3
    WHEN 'R' THEN 4
    WHEN 'NC-17' THEN 5
END;
```

```txt
+---------+-----------------------------+--------+
| film_id | title                       | rating |
+---------+-----------------------------+--------+
|       2 | ACE GOLDFINGER              | G      |
|       4 | AFFAIR PREJUDICE            | G      |
...
|       1 | ACADEMY DINOSAUR            | PG     |
|       6 | AGENT TRUMAN                | PG     |
...
|       7 | AIRPLANE SIERRA             | PG-13  |
|       9 | ALABAMA DEVIL               | PG-13  |
...
|       8 | AIRPORT POLLOCK             | R      |
|      17 | ALONE TRIP                  | R      |
...
|       3 | ADAPTATION HOLES            | NC-17  |
|      10 | ALADDIN CALENDAR            | NC-17  |
...
1000 rows in set (0.00 sec)
```

在这个例子中，我们使用 `CASE` 将电影的等级转换为一个索引数字。然后使用 `ORDER BY` 按照这个数字进行排序。

可能您觉得 `CASE` 子句写起来很复杂，特别是列表值很多的时候。那么，请使用如下的 `FIELD()` 函数。

### 使用 `FIELD()` 函数实现自定义排序

对于上面实例中的 `CASE` 语句，我们可以如下的使用 [`FIELD()`](https://www.sjkjc.com/mysql-ref/field/) 代替。

```sql
SELECT
    *
FROM
    film
ORDER BY FIELD(rating, 'G', 'PG', 'PG-13', 'R', 'NC-17');
```

输出结果与上面实例完全相同。

在本例中，我们使用 `FIELD(rating, 'G', 'PG', 'PG-13', 'R', 'NC-17')` 作为 `ORDER BY` 排序的表达式。其中 `FIELD(value, value1, value2, ...)` 函数返回 `value` 在 `value1, value2, ...` 列表中的位置。

### ORDER BY 和 NULL

在 MySQL 的升序排序中，NULL 值会出现在非 NULL 值之前。

```mysql
SELECT 'A' AS v
UNION ALL
SELECT 'B' AS v
UNION ALL
SELECT NULL AS v
UNION ALL
SELECT 0 AS v
UNION ALL
SELECT 1 AS v;

//output
+------+
| v    |
+------+
| A    |
| B    |
| NULL |
| 0    |
| 1    |
+------+

```

我们使用`ORDER BY`子句升序 ASC 排序时，NULL 排在非 NULL 值之前。

```mysql
SELECT *
FROM (
    SELECT 'A' AS v
    UNION ALL
    SELECT 'B' AS v
    UNION ALL
    SELECT NULL AS v
    UNION ALL
    SELECT 0 AS v
    UNION ALL
    SELECT 1 AS v
) t
ORDER BY v;

//output
+------+
| v    |
+------+
| NULL |
| 0    |
| 1    |
| A    |
| B    |
+------+
```

我们使用`ORDER BY`子句降序 DESC 排序时，NULL 排在非 NULL 值之后。

## LIMIT

`LIMIT`子句可用于限制`SELECT`语句返回的行数。`LIMIT`接收一个或两个非负数正数为参数。

```mysql
LIMIT [offset,] row_count;
//或者
LIMIT row_count OFFSET offset;
```

- 上述两种写法是等效的。
- `offset`指定返回的第一行偏移量。偏移量是相对于未使用 `LIMIT` 语句时的原始结果集而言的。`offset` 可理解为在原始结果集的基础上跳过的行数。
- `row_count`执行要返回的最大行数。
- `offset`是可选的，未指定`offset`时，默认的值为 0。
- `LIMIT`一般位于`SELECT`语句最后。

```mysql
SELECT
    select_expression, ...
FROM
    table_name
ORDER BY
    sort_expression, ...
LIMIT [offset,] row_count;
```

LIMIT 的一个很重要的应用就是分页查询。对于一些大型的数据表来说，分页查询能很好的减少数据库的消耗和提高用户体验。

`film` 表中共有 1000 行数据，这个我们可以通过以下的 `COUNT(*)` 查询得知。

```mysql
mysql> SELECT COUNT(*) FROM film;
+----------+
| COUNT(*) |
+----------+
|     1000 |
+----------+
1 row in set (0.00 sec)
```

## DISTINCT

当使用 `SELECT` 查询数据时，我们可能会得到一些重复的行。比如演员表中有很多重复的姓氏。如果想得到一个唯一的、没有重复记录的结果集，就需要用到 `DISTINCT` 关键字。

在 `SELECT` 语句中使用 `DISTINCT` 关键字会返回一个没有重复记录行的结果集。

```mysql
SELECT DISTINCT
	cloumns_list
FROM
	table_name
```

当 `DISTINCT` 遇到 `NULL` 值时，只保留一个 `NULL` 值。因为 `DISTINCT` 认为所有的 `NULL` 值都是相同的，这与字段的类型无关。

## JOIN

在 MySQL 中，`JOIN` 语句用于将数据库中的两个表或者多个表组合起来。

比如在一个学校系统中，有一个学生信息表和一个学生成绩表。这两个表通过学生 ID 字段关联起来。当我们要查询学生的成绩的时候，就需要连接两个表以查询学生信息和成绩。

### MySQL 链接类型

- 内部链接（INNER JOIN）
- 左连接（LEFT JOIN）
- 右链接（RIGHT JOIN）
- 交叉链接（CROSS JOIN）

MySQL 不支持全连接（FULL ORDER JOIN）。

### 样例表创建和插入数据

在 sakila 中，创建 student 和 student_score 表。

```mysql
//创建student和student_score
CREATE TABLE `student` (
  `student_id` int NOT NULL,
  `name` varchar(45) NOT NULL,
  PRIMARY KEY (`student_id`)
);

CREATE TABLE `student_score` (
  `student_id` int NOT NULL,
  `subject` varchar(45) NOT NULL,
  `score` int NOT NULL
);

//分别在两个表中加入数据
INSERT INTO `student` (`student_id`, `name`)
VALUES (1,'Tim'),(2,'Jim'),(3,'Lucy');

INSERT INTO `student_score` (`student_id`, `subject`, `score`)
VALUES (1,'English',90),(1,'Math',80),(2,'English',85),
       (2,'Math',88),(5,'English',92);

```

### 交叉链接

交叉连接返回两个集合的笛卡尔积。也就是两个表中的所有的行的所有可能的组合。这相当于内连接没有连接条件或者连接条件永远为真。

如果一个有 `m` 行的表和另一个有 `n` 行的表，它们交叉连接将返回 `m * n` 行数据。

交叉连接 student 和 student_score 表：

```mysql
//显式的
SELECT
	student.*,
	student_score.*
FROM
	student CROSS JOIN student_score;

//隐式的
SELECT
	student.*,
	student_score.*
FROM
	student, student_score;

//输出一样

+------------+------+------------+---------+-------+
| student_id | name | student_id | subject | score |
+------------+------+------------+---------+-------+
|          3 | Lucy |          1 | English |    90 |
|          2 | Jim  |          1 | English |    90 |
|          1 | Tim  |          1 | English |    90 |
|          3 | Lucy |          1 | Math    |    80 |
|          2 | Jim  |          1 | Math    |    80 |
|          1 | Tim  |          1 | Math    |    80 |
|          3 | Lucy |          2 | English |    85 |
|          2 | Jim  |          2 | English |    85 |
|          1 | Tim  |          2 | English |    85 |
|          3 | Lucy |          2 | Math    |    88 |
|          2 | Jim  |          2 | Math    |    88 |
|          1 | Tim  |          2 | Math    |    88 |
|          3 | Lucy |          5 | English |    92 |
|          2 | Jim  |          5 | English |    92 |
|          1 | Tim  |          5 | English |    92 |
+------------+------+------------+---------+-------+
15 rows in set (0.00 sec)
```

### 内连接

内连接基于连接条件组合两个表中的数据。内连接相当于加了过滤条件的交叉连接。

内连接将第一个表的每一行与第二个表的每一行进行比较，如果满足给定的连接条件，则将两个表的行组合在一起作为结果集中的一行。

![inner-join](.\assets\inner-join.png)

```mysql
SELECT
  student.*,
  student_score.*
FROM
  student
  INNER JOIN student_score
  ON student.student_id = student_score.student_id;

//Output
+------------+------+------------+---------+-------+
| student_id | name | student_id | subject | score |
+------------+------+------------+---------+-------+
|          1 | Tim  |          1 | English |    90 |
|          1 | Tim  |          1 | Math    |    80 |
|          2 | Jim  |          2 | English |    85 |
|          2 | Jim  |          2 | Math    |    88 |
+------------+------+------------+---------+-------+
4 rows in set (0.00 sec)
```

由于两个表都使用相同的字段进行等值匹配，因此您可以使用 `USING` 以下查询中所示的子句：

```mysql
SELECT
  student.*,
  student_score.*
FROM
  student
  INNER JOIN student_score USING(student_id);
```

### 左连接

左连接是左外连接的简称，左连接需要连接条件。

两个表左连接时，第一个表称为左表，第二表称为右表。例如 `A LEFT JOIN B`，`A` 是左表，`B` 是右表。

![left-join](.\assets\left-join.png)

左连接以左表的数据行为基础，根据连接匹配右表的每一行，如果匹配成功则将左表和右表的行组合成新的数据行返回；如果匹配不成功则将左表的行和 NULL 值组合成新的数据行返回。

```mysql
SELECT
  student.*,
  student_score.*
FROM
  student
  LEFT JOIN student_score
  ON student.student_id = student_score.student_id;

//Output
+------------+------+------------+---------+-------+
| student_id | name | student_id | subject | score |
+------------+------+------------+---------+-------+
|          1 | Tim  |          1 | Math    |    80 |
|          1 | Tim  |          1 | English |    90 |
|          2 | Jim  |          2 | Math    |    88 |
|          2 | Jim  |          2 | English |    85 |
|          3 | Lucy |       NULL | NULL    |  NULL |
+------------+------+------------+---------+-------+
5 rows in set (0.00 sec)
```

1. 结果集中包含了 `student` 表的所有记录行。
2. `student_score` 表中不包含 `student_id = 3` 的记录行，因此结果几种最后一行中来自 `student_score` 的列的内容为 `NULL`。
3. `student_score` 表存在多条 `student_id` 为 `1` 和 `2` 的记录，因此 `student` 表也产生了多行数据。

由于两个表都使用相同的字段进行等值匹配，因此您可以使用 `USING` 以下查询中所示的子句：

```mysql
SELECT
  student.*,
  student_score.*
FROM
  student
  LEFT JOIN student_score USING(student_id);
```

### 右连接

右连接是右外连接的简称，右连接需要连接条件。

右连接与左连接处理逻辑相反，右连接以右表的数据行为基础，根据条件匹配左表中的数据。如果匹配不到左表中的数据，则左表中的列为 `NULL` 值。

![right-join](.\assets\right-join.png)

```mysql
SELECT
  student.*,
  student_score.*
FROM
  student
  RIGHT JOIN student_score
  ON student.student_id = student_score.student_id;

  //Output
  +------------+------+------------+---------+-------+
| student_id | name | student_id | subject | score |
+------------+------+------------+---------+-------+
|          1 | Tim  |          1 | English |    90 |
|          1 | Tim  |          1 | Math    |    80 |
|          2 | Jim  |          2 | English |    85 |
|          2 | Jim  |          2 | Math    |    88 |
|       NULL | NULL |          5 | English |    92 |
+------------+------+------------+---------+-------+
5 rows in set (0.00 sec)
```

从结果集可以看出，由于左表中不存在到与右表 `student_id = 5` 匹配的记录，因此最后一行左表的列的值为 `NULL`。

右连接其实是左右表交换位置的左连接，即 `A RIGHT JOIN B` 就是 `B LEFT JOIN A`，因此右连接很少使用。

## GROUP BY

### 语法

在 MySQL 中， `GROUP BY` 子句用于将结果集根据指定的字段或者表达式进行分组。

有时候，我们需要将结果集按照某个维度进行汇总。这在统计数据的时候经常用到，考虑以下的场景：

- 按班级求取平均成绩。
- 按学生汇总某个人的总分。
- 按年或者月份统计销售额。
- 按国家或者地区统计用户数量。

```mysql
SELECT column1[, column2, ...], aggregate_function(ci)
FROM table
[WHERE clause]
GROUP BY column1[, column2, ...];
[HAVING clause]
```

- `column1[, column2, ...]` 是分组依据的字段，至少一个字段，可以多个字段。
- `aggregate_function(ci)` 是聚合函数。这是可选的，但是一般都用得到。
- `SELECT` 后的字段必须是分组字段中的字段。
- `WHERE` 子句是可选的，用来过滤结果集中的数据。
- `HAVING` 子句是可选的，用来过滤分组数据。

经常使用的聚合函数主要有：

- [`SUM()`](https://www.sjkjc.com/mysql-ref/sum/): 求总和
- [`AVG()`](https://www.sjkjc.com/mysql-ref/avg/): 求平均值
- [`MAX()`](https://www.sjkjc.com/mysql-ref/max/): 求最大值
- [`MIN()`](https://www.sjkjc.com/mysql-ref/min/): 求最小值
- [`COUNT()`](https://www.sjkjc.com/mysql-ref/count/): 计数

### 案例

```mysql
//我们使用 GROUP BY 子句和聚合函数 COUNT() 查看 actor 表中的姓氏列表以及每个姓氏的次数。
SELECT last_name, COUNT(*)
FROM actor
GROUP BY last_name
ORDER BY COUNT(*) DESC;

//Output
+--------------+----------+
| last_name    | COUNT(*) |
+--------------+----------+
| KILMER       |        5 |
| NOLTE        |        4 |
| TEMPLE       |        4 |
| AKROYD       |        3 |
| ALLEN        |        3 |
| BERRY        |        3 |
...
| WRAY         |        1 |
+--------------+----------+
121 rows in set (0.00 sec)

//本例执行顺序如下
//1.首先使用 GROUP BY 子句按照last_name字段对数据进行分组。
//2.然后使用聚合函数 COUNT(*) 汇总每个姓氏的行数。
//3.最后使用 ORDER BY 子句按照 COUNT(*) 降序排列。

//以下实例使用 GROUP BY 子句，LIMIT 子句和聚合函数 SUM() 返回总消费金额排名前 10 位的客户。
SELECT customer_id, SUM(amount) total
FROM payment
GROUP BY customer_id
ORDER BY total DESC
LIMIT 10;

//Output
+-------------+--------+
| customer_id | total  |
+-------------+--------+
|         526 | 221.55 |
|         148 | 216.54 |
|         144 | 195.58 |
|         137 | 194.61 |
|         178 | 194.61 |
|         459 | 186.62 |
|         469 | 177.60 |
|         468 | 175.61 |
|         236 | 175.58 |
|         181 | 174.66 |
+-------------+--------+
10 rows in set (0.02 sec)

//1.首先使用 GROUP BY 子句按照customer_id字段对数据进行分组。
//2.然后使用聚合函数 SUM(amount) 汇总每个用户的amount字段，并使用total作为别名
//3.然后使用 ORDER BY 子句按照total降序排列。
//4.最后使用 LIMIT 10 子句返回前10个记录行。
```

## UNION

在 MySQL 中，`UNION` 操作符是一个集合操作符，它用于合并 2 个结果集中的所有的行。

SQL 标准中定义了 3 个集合操作符： `UNION`, `INTERSECT` 和 `MINUS`。目前 MySQL 只支持 `UNION`。

### 语法

`UNION` 操作符用来合并两个 `SELECT` 语句的结果集。

```mysql
SELECT statement
UNION [DISTINCT | ALL]
SELECT statement
```

- `UNION` 双目操作符，需要两个 `SELECT` 语句作为操作数。
- `UNION` 中的 `SELECT` 语句中的列数、列顺序必须相同。
- `UNION` 运算包括 `UNION DISTINCT` 和 `UNION ALL` 两种算法，其中 `UNION DISTINCT` 可以简写为 `UNION`。
- `UNION DISTINCT` 或 `UNION` 将过滤掉结果集中重复记录。
- `UNION ALL` 将返回结果集中的所有记录。

### 案例

```mysql
CREATE TABLE a (v INT);
CREATE TABLE b (v INT);
CREATE TABLE c (v INT);

INSERT INTO a VALUES (1), (2), (NULL), (NULL);
INSERT INTO b VALUES (2), (2), (NULL);
INSERT INTO c VALUES (3), (2);

//对从a和b表返回的两个结果集进行union运算
SELECT * FROM a
UNION
SELECT * FROM b;

//对三个表返回的结果集进行union运算
SELECT * FROM a
UNION
SELECT * FROM b
SELECT * FROM c;

//对前两个表进行union运算，返回的结果集和第三个表进行union all运算
SELECT * FROM a
UNION
SELECT * FROM b
UNION ALL
SELECT * FROM c;
//首先对a和b表的记录进行 UNION 运算，并返回结果集。这一步运算删除了a和b表的重复记录。
//将第1步的结果集和c表的记录进行 UNION ALL 运算。这一步并没有删除c表中与第1步结果集中的重复记录。

```

### UNION 别名

参与 `UNION` 运算的结果集只要列数一样就可以。返回结果集的列名采用第一个结果集的列名。

```mysql
//分别看列名
select 1;
select 2;
select 1, 2;

//第三个Output
+---+
| 1 |
+---+
| 1 |
| 2 |
+---+
2 rows in set (0.00 sec)

SELECT 2 AS c
UNION
SELECT 1;

//Output
+---+
| c |
+---+
| 2 |
| 1 |
+---+
2 rows in set (0.00 sec)
```

## 别名

如果在一个 SQL 中涉及到多个表，我们需要使用 `table_name.column_name` 这样的方式来引用每个表的字段，这有时候会让 SQL 变的臃肿和难以阅读。而使用别名，则可以简化 SQL 和提高 SQL 的可读性。

在 MySQL 中，我们可以使用的别名包括： 列别名，表别名和派生表别名。

### 列别名

```mysql
SELECT column_name, AS `alias`
FROM table_name;
```

- `AS` 关键字后面跟的是列的别名 `alias`
- 当别名 `alias` 中包含空格时，必须使用 `` 将别名引起来，即`alias ``。
- `AS` 关键字是可选的。
- 除了为字段指定别名，还可以为表达式指定别名。例如： `SELECT NOW() `Current Time` FROM dual`

```mysql
SELECT
		first_name,
		last_name,
		CONCAT(first_name, ', ', last_name)
FROM
		actor
LIMIT 5;

//Output
+------------+--------------+-------------------------------------+
| first_name | last_name    | CONCAT(first_name, ', ', last_name) |
+------------+--------------+-------------------------------------+
| PENELOPE   | GUINESS      | PENELOPE, GUINESS                   |
| NICK       | WAHLBERG     | NICK, WAHLBERG                      |
| ED         | CHASE        | ED, CHASE                           |
| JENNIFER   | DAVIS        | JENNIFER, DAVIS                     |
| JOHNNY     | LOLLOBRIGIDA | JOHNNY, LOLLOBRIGIDA                |
+------------+--------------+-------------------------------------+
5 rows in set (0.00 sec)

SELECT
		first_name `First Name`,
		last_name `Last Name`,
		CONCAT(first_name, ', ', last_name) `Full Name`
FROM
		actor
LIMIT 5;

//Output
+------------+--------------+----------------------+
| First Name | Last Name    | Full Name            |
+------------+--------------+----------------------+
| PENELOPE   | GUINESS      | PENELOPE, GUINESS    |
| NICK       | WAHLBERG     | NICK, WAHLBERG       |
| ED         | CHASE        | ED, CHASE            |
| JENNIFER   | DAVIS        | JENNIFER, DAVIS      |
| JOHNNY     | LOLLOBRIGIDA | JOHNNY, LOLLOBRIGIDA |
+------------+--------------+----------------------+
5 rows in set (0.00 sec)
```

在本例中，我们为以下列指定了别名：

- 指定 `first_name` 列的别名为 `First Name`。
- 指定 `last_name` 列的别名为 `Last Name`。
- 指定 `CONCAT(first_name, ', ', last_name)` 表达式的别名为 `Full Name`。

### 表别名

```mysql
table_name AS alias
//其中 AS 关键字是可选的，因此您可以省略它。

SELECT *
FROM language
WHERE EXISTS(
    SELECT *
    FROM film
    WHERE film.language_id = language.language_id
  );

//上面的例子中，没有使用表别名，我们使用 film.language_id 和 language.language_id 分别引用 film 和 language 的 language_id 字段。

SELECT *
FROM language l
WHERE EXISTS(
    SELECT *
    FROM film f
    WHERE f.language_id = l.language_id
  );
```

### 派生别名

派生表是一个由表达式生成的表，使用派生表就像使用表一样。常见的派生表由子查询产生。

```mysql
SELECT
    AVG(t.customer_total) customer_avg
FROM
    (SELECT
        customer_id, SUM(amount) customer_total
    FROM
        payment
    GROUP BY customer_id
    HAVING customer_total > 180) t;

//Output
+--------------+
| customer_avg |
+--------------+
| 201.585000 |
+--------------+
1 row in set (0.02 sec)
//在本例中，我们为派生表:
(SELECT
    customer_id, SUM(amount) customer_total
FROM
    payment
GROUP BY customer_id
HAVING customer_total > 180)
指定了别名t
使用派生表必须指定别名。因为，FROM 子句中的所有表都必须有一个名字。
```

## 子查询

MySQL 子查询是嵌套一个语句中的查询语句，也被称为内部查询。子查询经常用在 [`WHERE`](https://www.sjkjc.com/mysql/where/) 子句中。例如:

```mysql
SELECT *
FROM language
WHERE EXISTS(
    SELECT *
    FROM film
    WHERE film.language_id = language.language_id
  );

//其中
SELECT *
FROM film
WHERE film.language_id = language.language_id
这是一个子查询，它是EXISTS子句的参数，用来查询符合条件的数据行。
```

### 派生表

当一个子查询位于 `FORM` 子句中时，这个子查询被称为派生表。

```mysql
SELECT *
FROM (
    SELECT last_name,
      COUNT(*) count
    FROM actor
    GROUP BY last_name
  ) t
WHERE t.last_name LIKE 'A%';

//其中第一个FROM括号内的语句就是一个派生表，并且有一个别名t。派生表必须使用别名，因为 MySQL 规定，任何 FORM 子句中的表必须具有一个名字。
```

请注意，派生表不是临时表。

派生表遵循以下规则：

- 派生表必须具有别名。
- 派生表的列名必须是唯一的。

## INSERT

### 语法

在 MySQL 中，`INSERT` 语句用于将一行或者多行数据插入到数据表中。

```mysql
//插入单行数据
INSERT INTO table_name (column_1, column_2, ...)
VALUES (value_1, value_2, ...);

//插入多行数据
INSERT INTO table_name (column_1, column_2, ...)
VALUES (value_11, value_12, ...),
       (value_21, value_22, ...)
       ...;
```

- `INSERT INTO` 和 `VALUES` 都是关键字。
- `INSERT INTO` 后跟表名 `table_name`。
- 表名 `table_name` 后跟要插入数据的列名列表。列名放在小括号中，多个列表使用逗号分隔。
- `VALUES` 关键字之后的小括号中是值列表。值的数量要和字段的数量相同。值的位置和列的位置一一对应。
- 当插入多行数据时，多个值列表之间使用逗号分隔。
- `INSERT` 语句返回插入的行数。

```mysql
//例子
CREATE TABLE user (
    id INT AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    age INT,
    birthday DATE,
    PRIMARY KEY (id)
);

//插入单行数据
INSERT INTO user (name, age)
VALUES ("Jim", 18);

//插入后输出中的 1 row affected 代表已经成功插入了一行数据到 user 表中
//id 列的值是自动生成的，因为它是 AUTO_INCREMENT 列。
//birthday 列值为 NULL，因为我们只插入了 name 和 age 列。

//插入多行数据
INSERT INTO user (name, age)
VALUES ("Tim", 19), ("Lucy", 16);

//Output
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

//输出中的 2 row affected 代表已经成功插入了 2 行数据到 user 表中。
//Records: 2 代表有 2 行数据要插入到表中。
//Duplicates: 0 代表重复的行数是 0。
//Warnings: 0 代表需要注意的行数是 0。


```

### INSERT 修饰符

在 MySQL 中， `INSERT` 语句支持 4 个修饰符:

- `LOW_PRIORITY`: 如果你指定了 `LOW_PRIORITY` 修饰符，MySQL 服务器将延迟执行 `INSERT` 操作直到没有客户端对表进行读操作。

  `LOW_PRIORITY` 修饰符影响那些只支持表级锁的存储引擎，比如： `MyISAM`, `MEMORY`, 和 `MERGE`。

- `HIGH_PRIORITY`: 如果你指定了 `HIGH_PRIORITY` 修饰符，它会覆盖掉服务器启动时的 `--low-priority-updates` 选项。

  `HIGH_PRIORITY` 修饰符影响那些只支持表级锁的存储引擎，比如： `MyISAM`, `MEMORY`, 和 `MERGE`。

- `IGNORE`: 如果你指定了 `IGNORE` 修饰符，MySQL 服务器会在执行 `INSERT` 操作期间忽略那些可忽略的错误。这些错误最终会作为 `WARNING` 返回。

- `DELAYED`: 这个修饰符已经在 MySQL 5.6 版本中弃用，将来会被删除。在 MySQL 8.0 中，这个修饰符可用但会被忽略。

用法：

```mysql
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
INTO table_name
...
```

### INSERT 限制

在 MySQL 中，`max_allowed_packet` 配置了服务器和客户端任何单个消息大小的上限。这同样适用于 `SELECT` 语句。当一个 `SELECT` 语句的大小超过 `max_allowed_packet` 值时，服务器就会给出一个错误。

以下语句显示了当前服务器上的 `max_allowed_packet` 配置：

```mysql
SHOW VARIABLES LIKE 'max_allowed_packet';
```

`max_allowed_packet` 以字节为单位。并且不同服务器上的值可能不同。

## DELETE

### 语法

如果要从数据库表中删除记录行，请使用 `DELETE` 语句。 `DELETE` 语句单表删除语法如下：

```mysql
DELETE FROM table_name
[WHERE clause]
[ORDER BY ...]
[LIMIT row_count]
```

- `DELETE FROM` 后跟的是要从中删除数据的表。
- `WHERE` 子句用来过滤需要删除的行。满足条件的行会被删除。
- `WHERE` 子句是可选的。没有 `WHERE` 子句时，`DELETE` 语句将删除表中的所有行。
- `ORDER BY` 子句用来指定删除行的顺序。它是可选的。
- `LIMIT` 子句用来指定删除的最大行数。它是可选的。
- `DELETE` 语句返回删除的行数。

`DELETE` 语句中的 `WHERE` 子句非常重要。除非您特意，否则不要省略 `WHERE` 子句。它会导致删除表中的所有数据。

如果单独使用 `LIMIT` 子句，删除的顺序是不明确的。大多数情况下， `DELETE` 语句中的 `LIMIT` 子句都应该和 `ORDER BY` 子句一起使用。

如果我们不在 `DELETE` 语句中使用 `WHERE` 或者 `LIMIT` 子句，则会删除表中的所有行。

如果你只是想清空表，可以使用 [`TRUNCATE TABLE`](https://www.sjkjc.com/mysql/table-truncate/) 语句以获得更好的性能。如下：

### delete 表别名

在早期的 MySQL 版本中， 单表删除 `DELETE` 语句不支持为表设置别名。

```mysql
DELETE FROM main_table m
WHERE NOT EXISTS (
    SELECT *
    FROM another_table a
    WHERE a.main_id = m.id
  );
//会报错，改成：
DELETE m FROM main_table m
WHERE NOT EXISTS (
    SELECT *
    FROM another_table a
    WHERE a.main_id = m.id
  );
//或者不适用别名，而是使用表名
DELETE FROM main_table
WHERE NOT EXISTS (
    SELECT *
    FROM another_table a
    WHERE a.main_id = main_table.id
  );

```

### DELETE 多表删除

我们也可以在一个 `DELETE` 语句中指定多个表，以便在一个或多个表中删除符合 `WHERE` 子句中的条件的行。

```mysql
//以下语句删除 t1 和 t2 表中满足条件的行：
DELETE t1, t2
FROM t1 INNER JOIN t2
WHERE t1.id = t2.id;

//以下语句删除 t1 表中满足条件的行：
DELETE t1
FROM t1 INNER JOIN t2
WHERE t1.id = t2.id;

//以下语句在删除时使用 LEFT JOIN
DELETE t1
FROM
  t1 LEFT JOIN t2 ON t1.id = t2.id
WHERE t2.id IS NULL;
```

只要是 [`SELECT`](https://www.sjkjc.com/mysql/select/) 语句中允许使用的 [`JOIN`](https://www.sjkjc.com/mysql/join/) 类型，多表删除语句都可以使用。

多表删除语句中不能使用 `LIMIT` 子句和 `ORDER BY` 子句。

在 MySQL 中， `DELETE` 语句支持 3 个修饰符:

- `LOW_PRIORITY`: 如果你指定了 `LOW_PRIORITY` 修饰符，MySQL 服务器将延迟执行 `DELETE` 操作直到没有客户端对表进行读操作。这个修饰符影响那些只支持表级锁的存储引擎，比如： `MyISAM`, `MEMORY`, 和 `MERGE`。
- `QUICK`: 如果你指定了 `QUICK` 修饰符，`MyISAM` 存储引擎不会在 `DELETE` 操作期间合并索引。这在某种程度上会加快 `DELETE` 操作。
- `IGNORE`: 如果你指定了 `IGNORE` 修饰符，MySQL 服务器会在执行 `DELETE` 操作期间忽略那些可忽略的错误。这些错误最终会作为 `WARNING` 返回。

```mysql
DELETE [LOW_PRIORITY] [QUICK] [IGNORE] FROM table_name
```

## UPDATE

### 语法

`UPDATE` 语句可以更新表中的一行或者多行数据，可以更新表中的一个或者多个字段（列）。

```mysql
UPDATE [IGNORE] table_name
SET
    column_name1 = value1,
    column_name2 = value2,
    ...
[WHERE clause];
```

- `UPDATE` 关键字后指定要更新数据的表名。
- 使用 `SET` 子句设置字段的新值。多个字段使用逗号分隔。字段的值可以是普通的字面值，也可以是表达式运算，还可以是子查询。
- 使用 [`WHERE`](https://www.sjkjc.com/mysql/where/) 子句指定要更新的行。只有符合 `WHERE` 条件的行才会被更新。
- `WHERE` 子句是可选的。如果不指定 `WHERE` 子句，则更新表中的所有行。

`UPDATE` 语句中的 `WHERE` 子句非常重要。除非您特意，否则不要省略 `WHERE` 子句。

### 表达式更新

使用 `UPDATE` 更新时，字段的值可以设置为表达式的运算结果，比如函数或其他的运算。

下面的 `UPDATE` 更新所有客户的电子邮件的域名部分：

```mysql
UPDATE customer
SET email = REPLACE(email, 'sakilacustomer.org', 'sjkjc.com');
```

### 使用子查询更新

下面实例展示了如何为没有绑定商店的客户绑定一个随机商店。

```mysql
UPDATE customer
SET store_id = (
    SELECT store_id
    FROM store
    ORDER BY RAND()
    LIMIT 1
  )
WHERE store_id IS NULL;
```

在 `SET` 子句中，将 `store_id` 的值设置为上面的子查询。

### UPDATE 修饰符

在 MySQL 中， `UPDATE` 语句支持 2 个修饰符:

- `LOW_PRIORITY`: 如果你指定了 `LOW_PRIORITY` 修饰符，MySQL 服务器将延迟执行 `UPDATE` 操作直到没有客户端对表进行读操作。

  `LOW_PRIORITY` 修饰符影响那些只支持表级锁的存储引擎，比如： `MyISAM`, `MEMORY`, 和 `MERGE`。

- `IGNORE`: 如果你指定了 `IGNORE` 修饰符，MySQL 服务器会在执行 `UPDATE` 操作期间忽略那些可忽略的错误。这些错误最终会作为 `WARNING` 返回。

修饰符的用法如下：

```mysql
UPDATE [LOW_PRIORITY] [IGNORE] table_name SET column_name = value
```

## REPLACE

`REPLACE`语句和`INSERT`语句很想，他们的不同之处在于，当插入过程中出现了重复的逐渐或者重复的唯一索引的时候，`INSERT`语句惠灿盛一个错误，而`REPLACE`语句则先删除旧的行，再插入新的行。

`REPLACE`语句不在标准 SQL 的范畴。

### REPLACE 语法

我们可以使用一个`REPLACE` 语句插入一行或多行数据。

```mysql
REPLACE [INTO] table_name (column_1, column_2, ...)
VALUES  (value_11, value_12, ...)
		(value_21, value_22, ...)
		...;
```

- `REPLACE INTO` 和 `VALUES` 都是关键字。`INTO` 可省略。
- `REPLACE INTO` 后跟表名 `table_name`。
- 表名 `table_name` 后跟要插入数据的列名列表。列名放在小括号中，多个列表使用逗号分隔。
- `VALUES` 关键字之后的小括号中是值列表。值的数量要和字段的数量相同。值的位置和列的位置一一对应。
- 当插入多行数据时，多个值列表之间使用逗号分隔。

`REPLACE` 语句还可以使用 `SET` 关键词，这只适用于操作单行。

```mysql
REPLACE [INTO] table_name
SET column_1 = value1,
	column_2 = value2,
	...;
```

​
