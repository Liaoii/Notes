# MySQL文件结构

MySQL是通过文件系统对数据和索引进行存储的，从物理结构上可以分为**日志文件**和**数据索引文件**。

在Linux系统中，MySQL的数据索引文件和日志文件通常放在 `/var/lib/mysql` 目录下。

## 1. 日志文件

MySQL通过日志记录了数据库操作信息和错误信息，常用的日志文件包括**错误日志**、**二进制日志**、**查询日志**、**慢查询日志**和**事务Redo日志**、**中继日志**等。

查看当前数据库中的日志使用信息：

```mysql
mysql> show variables like 'log_%';

+----------------------------------------+---------------------+
| Variable_name                          | Value               |
+----------------------------------------+---------------------+
| log_bin                                | OFF                 |
| log_bin_basename                       |                     |
| log_bin_index                          |                     |
| log_bin_trust_function_creators        | OFF                 |
| log_bin_use_v1_row_events              | OFF                 |
| log_builtin_as_identified_by_password  | OFF                 |
| log_error                              | /var/log/mysqld.log |
| log_error_verbosity                    | 3                   |
| log_output                             | FILE                |
| log_queries_not_using_indexes          | OFF                 |
| log_slave_updates                      | OFF                 |
| log_slow_admin_statements              | OFF                 |
| log_slow_slave_statements              | OFF                 |
| log_statements_unsafe_for_binlog       | ON                  |
| log_syslog                             | OFF                 |
| log_syslog_facility                    | daemon              |
| log_syslog_include_pid                 | ON                  |
| log_syslog_tag                         |                     |
| log_throttle_queries_not_using_indexes | 0                   |
| log_timestamps                         | UTC                 |
| log_warnings                           | 2                   |
+----------------------------------------+---------------------+
```

### 1.1 错误日志（errorlog）

MySQL默认开启了错误日志，而且从MySQL 5.5.7以后无法关闭错误日志，错误日志记录了运行过程中遇到的所有严重的错误信息,以及MySQL每次启动和关闭的详细信息。

可以在my.cnf配置文件中通过log-error和log-warnings参数对错误日志进行配置，其中log-error参数用于指定错误日志的名称，log-warnings参数用于定义是否将警告信息也写入到错误日志中。

```bash
# 指定错误日志文件名，其路径为/var/lib/文件名.err
log-error=/var/log/mysqld.log
# 可以取值0-2，其中0代表关闭，1代表开启
log-warnings=2
```

注：经过尝试，log-error的值也可以带文件后缀名，但是在这种情况下，其值只能为`/var/log/mysqld.log`或者`/var/log/mysql.log`，在不带文件后缀时不可以有文件路径，只能为单个文件名，系统会在`/var/lib`路径下生成`文件名.err`文件。

### 1.2 二进制日志（binlog）

二进制日志默认是关闭的，如果想要开启需要在配置文件中进行配置：

```bash
log-bin=mysql-bin
```

其中mysql-bin是二进制日志文件的基本名称，二进制日志文件的完整名称为：mysql-bin-000001.log

binlog记录了数据库所有的DDL语句和DML语句，但不包括SELECT语句的内容，文件中的语句以事件的形式保存，描述了数据的变更顺序，binlog还包括了每个更新语句的执行时间信息。如果是DDL语句，则直接记录到binlog中，而DML语句，必须通过事务提交才能记录到binlog中。

binlog主要用于实现MySQL主从复制、数据备份、数据恢复。

### 1.3 通用查询日志（general query log）

通用查询日志在默认情况下是关闭的。

通用查询日志会记录用户的所有操作，包括增、删、改、查等信息，在并发量很大的环境下会产生大量的日志信息而导致不必要的磁盘IO，这会影响MySQL的性能，所以如果不是为了调试数据库建议不要开启通用查询日志。

查看通用查询日志是否开启：

```mysql
mysql> show global variables like "general_log";
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| general_log   | OFF   |
+---------------+-------+
```

可以在配置文件中配置开启：

```bash
# 是否开启
general_log={ON|OFF}
# 指定文件名称，默认名称为host_name.log
general_log_file=general.log
# 记录类型
log_output={TABLE|FILE|NONE}
```

### 1.4 慢查询日志（slow query log）

慢查询日志在默认情况下是关闭的。

可以在配置文件中配置开启：

```bash
# 是否开启慢查询日志
slow_query_log={OFF|ON}
# 慢查询的阈值
long_query_time=10
# 慢查询日志文件名称，默认为host_name-slow.log
slow_query_log_file=file_name
```

如果开启了慢查询日志，它会记录执行时间超过了long_query_time的所有查询，写入到slow_query_log_file日志文件中，这样我们就可以收集查询时间比较长的SQL语句。

查看有多少SQL语句超过了慢查询设置的时间阈值：

```mysql
mysql> SHOW GLOBAL STATUS LIKE '%Slow_queries%';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Slow_queries  | 0     |
+---------------+-------+
```

## 2. 数据文件

查看MySQL的数据文件：

```mysql
mysql> SHOW VARIABLES LIKE '%datadir%';
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| datadir       | /var/lib/mysql/ |
+---------------+-----------------+
```

### 2.1 InnoDB数据文件

- .frm文件：用于存储与表相关的数据信息，主要包括表结构的定义信息。
- .ibd文件：用于存储表数据和索引信息，一张表对应一个ibd文件，其使用独享的表空间。
- ibdata文件：用于存储表数据和索引信息，所有表共同使用一个或多个ibdata文件，其使用的是共享表空间。

### 2.2 MyIsam数据文件

- .frm文件：用于存储与表相关的数据信息，主要包括表结构的定义信息。
- .myd文件：用于存储表的数据信息。 
- .myi文件：用于存储表的索引信息。
