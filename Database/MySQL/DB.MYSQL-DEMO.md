---
layout: default
title: MySQL Type
parent: MySQL
grand_parent: Database
nav_order: 11
---

## MYSQL全部类型

### Mysql字符集：

utf8与utf8mb4（utf8 most bytes 4）
 MySQL 5.5.3之后增加了utfmb4字符编码
 utf8mb4是utf8的超集并完全兼容utf8，能够用四个字节存储更多的字符（如emoji表情😀😁😂）。

mysql数据库编码格式utf8_general_ci、utf8_bin、utf8_general_cs区别：

- utf8_bin将字符串中的每一个字符用二进制数据存储，区分大小写。（bin -> binary二进制的意思）

- utf8_general_ci不区分大小写，ci为case insensitive的缩写，即大小写不敏感。

- utf8_general_cs区分大小写，cs为case sensitive的缩写，即大小写敏感。

### Mysql 字段类型

（一）整型数值：

| 整数类型 | 字节数 | 最小值 ~ 最大值 |
| -------- | ------ | --------------- |
|tinyint	|1	|-128~127 或 0-255|
|smallint	|2	|-32768~32767 或 0~65535|
|mediumint	|3	|-8388608~8388607 或 0~1677215|
|int	|4	|-2147483648~2147483647 或 0~4294967295|
|bigint	|8	|-9223372036854775808~9223372036854775807 或 0~18446744073709551615|

（二）浮点数类型
|浮点数类型	|字节数	|最小值 ~ 最大值|
| -------- | ------ | --------------- |
||double	|4	|±1.175494351E-38 ~ ± 3.402823466E+38|
|double	|8	|±2.2250738585072014E-308 ~ ±1.7976931348623157E+308|

（三）位类型
|定点数类型	|字节数	|最小值 ~ 最大值|
| -------- | ------ | --------------- |
|dec(m,d)	|m+2	|最大取值范围与double相同，给定decimal的有效值取值范围由m和d决定|

（四）定点数类型
| 位类型	| 字节数	| 最小值 ~ 最大值|
| -------- | ------ | --------------- |
|bit(m)	|1~8	|bit(1) ~ bit(64)|

（五）日期时间类型
|时间日期类型	|字节数	|最小值 ~ 最大值|
| -------- | ------ | --------------- |
|date	|4	|1000-01-01 ~ 9999-12-31|
|datetime	|8	|1000-01-01 00:00:00 ~ 9999-12-31 23:59:59|
|timestamp	|4	|19700101080001 ~ 2038年某个时刻|
|time	|3	|-838:59:59 ~ 838:59:59|
|year	|1	|1901 ~ 2155|

（六）字符串类型：
| 字符串类型	| 字节数	| 取值范围|
| -------- | ------ | --------------- |
| char(m)	| m	| m为0 ~ 255之间的整数|
| varchar(m)	| 值长度+1	| m为0~65535之间的整数|
| tinytext	| 值长度+2	| 允许长度0~255字节|
| text	| 值长度+2	| 允许长度0~65535字节|
| mediumtext	| 值长度+3	| 允许长度0~167772150字节|
| longtext	| 值长度+3	| 允许长度0~4294967295字节|
| binary(m)	| m	| 允许0~m个字节定长的字符串|
| varbinary(m)	| 值长度+1	| 允许0~m个字节变长的字符串|
| tinyblob	| 值长度+1	| 允许长度0~255字节|
| blob	| 值长度+2	| 允许长度0~65535字节|
| mediumblob	| 值长度+3	| 允许长度0~167772150字节|
| longblob	| 值长度+4	| 允许长度0~4294967295字节|
| enum	| 1或2	| 1255个成员需要1个字节存；25565535个成员，2个字节存|
| set	| 1/2/3/4/8	| 类似enum,set一次可以选取多个成员，而enum只能一个|

[【官方类型】](https://dev.mysql.com/doc/refman/8.0/en/data-types.html) 