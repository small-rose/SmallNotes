---
layout: default
title: OceanBase 
nav_order: 60
parent: Database
---

# OceanBase Learn
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

--- 

### OceanBase v3.2.3.3


## OB 兼容

通用问题

经测试 OB 不支持 `$$PLSQL_UNIT` 
解决办法：

（1）人工修改

(2）导出包体脚本后可执行批量替换。

1、SqI-20221208-01-AMS_BASE_DB_ACCT_PKG

for i in 1 .. C_BASE_DB_ACCT_DEL.countO loop

去掉count后面去掉()改为for i in 1 .. C_BASE_DB_ACCT_DEL.count loop

2、Sql-20221208-02-AMS_POLICYINSURANCEMATCH_PKG

无效的字符：

將中文字符修改成英文。

3、Sql-20221208-03-AMS_POM_GATHER_TD_NEW_PKG(i-V_MONTH)

去掉括号改为i-V_MONTH

4、sql-20221208-04-mm_mirror_pkg_usabnogroup

约4217行ROUND((V_REMAINS)*v_rate,2)改为


## OB优化指导


1、OB库表的数据接近10亿条，必须要做表分区。


2、多台机器部署涉及到分布式操作时。

可以不使用OB负载均衡.

比如根据业务特征，表关联密切，人工管理表到指定机器节点，以减少高频操作的分布式跨机器执行，尽可能SQL执行效率（不是一定可以提高，具体还是要看业务特征。）

也可以根据业务特征，进行统一的表分区。进行表组绑定。让相同的分区字段的表分区在某些节点上，

比如上海分公司在节点1，江苏、浙江在节点2.

如果要建表组，必须是（1、分区表2、分区表的分区数一致）才。


3、OB不建议使用二级分区表。

二级分区表调整为一级分区表，需要自己手工整理出SQL脚本，压测环境和生产环境执行这种SQL脚本。

对索引变动影响

查询条件带上分区条件的时候，自动道指定分区检索。可以适当创建联合索引，一般使用本地索引。

- LOCAL索引：分区有多少个，就有多少份LOCAL索引，其实是分区内的索引
- GLOBAL索引： 一个索引，对所有的分区数据进行排序。涉及的跨分区排序的时候，可以使用。

二级分区表调整为一级分区表后，表分区不一样，对导数据有没有影响？
数据影响小


4、其他兼容问题

针对Oracle模式兼容Oracle问题。



需要sys租户下查看
oceanbase.__all_virtual_table表中的 index_type 字段判断索引的类型，其中字段值和索引类型如下
  INDEX_TYPE_NORMAL_LOCAL = 1, // 局部普通索引
  INDEX_TYPE_UNIQUE_LOCAL = 2, // 局部唯一索引
  INDEX_TYPE_NORMAL_GLOBAL = 3, // 全局普通索引
  INDEX_TYPE_UNIQUE_GLOBAL = 4, // 全局唯一索引
  INDEX_TYPE_PRIMARY = 5,
  INDEX_TYPE_DOMAIN_CTXCAT = 6, // 全文索引
  INDEX_TYPE_NORMAL_GLOBAL_LOCAL_STORAGE = 7, // 全局索引，局部存储
  INDEX_TYPE_UNIQUE_GLOBAL_LOCAL_STORAGE = 8, // 全局索引，局部存储
  
  其中INDEX_TYPE_NORMAL_GLOBAL和INDEX_TYPE_UNIQUE_GLOBAL是全局索引，其他类型都为局部索引
对于非分区表，OB内部对建索引做了一个优化，对外表现是GLOBAL索引，实质上是创建的LOCAL索引，
对应的TYPE就是INDEX_TYPE_NORMAL_GLOBAL_LOCAL_STORAGE，index_type=3和index_typ=4是真实的全局索引，
索引有自己独立的分区


## 表组

一.表组相关命令

```sql
show tablegroups;--查询所有表组

show tablegroups where tablegroup_name='TG_1';--查询表组名为TG_1的表组信息

SHOW TABLEGROUPS WHERE Tablegroup_name='TG_1';--查询表组TG_1的分区信息

DROP TABLEGROUP tblgroup1;--删除表组

ALTER TABLE fbs1 SET TABLEGROUP '';--把fbsl移出表组

```

二、建模拟一级分区表，建分区表组。

结论：分区表组和分区表组内的分区表分区结构必须要统一，分区键名称可以不一样

1.新建分区表

```sql
create table fbs1
(
SUBCOMPANY varchar2(10),
fgs varchar2(20),
classcode varchar2(20)
)
 partition by list("SUBCOMPANY")
(partition SUBCOMPANY_1010100 values  ('1010100'),
partition SUBCOMPANY_1020100 values  ('1020100'),
partition SUBCOMPANY_OTHER values  (DEFAULT));

create table fbs2
(
SUBCOMPANY varchar2(10),
fgs varchar2(20),
risktype varchar2(20)
)
 partition by list("SUBCOMPANY")
(partition SUBCOMPANY_1010100 values  ('1010100'),
partition SUBCOMPANY_1020100 values  ('1020100'),
partition SUBCOMPANY_OTHER values  (DEFAULT));

create table fbs4
(
SUBCOMPANY varchar2(10),
fgs varchar2(20),
risktype1 varchar2(20)
)
 partition by list("SUBCOMPANY")
(partition SUBCOMPANY_1010100 values  ('1010100'),
partition SUBCOMPANY_1020100 values  ('1020100'),
partition SUBCOMPANY_OTHER values  (DEFAULT));

create table fbs3
(
FGSDM varchar2(10),
fgs varchar2(20),
risktype varchar2(20)
)
 partition by list("FGSDM")
(partition FGSDM_1010100 values  ('1010100'),
partition FGSDM_1020100 values  ('1020100'),
partition FGSDM_OTHER values  (DEFAULT));

create table fbs5
(
FGSDM varchar2(10),
fgs varchar2(20),
risktype1 varchar2(20)
)
 partition by list("FGSDM")
(partition FGSDM_1010100 values  ('1010100'),
partition FGSDM_1020100 values  ('1020100'),
partition FGSDM_1030100 values  ('1030100'));
```

2.新建分区表组

```sql
CREATE TABLEGROUP tg_1 PARTITION BY LIST 1  
(PARTITION SUBCOMPANY_1010100 VALUES ('1010100'), 
PARTITION SUBCOMPANY_1020100 VALUES ('1020100'),
partition SUBCOMPANY_OTHER values  (DEFAULT));
```


3.给分区表组添加分区表
```sql

ALTER TABLE fbs4 TABLEGROUP = tg_1;--单分区表加入表组的方法。不报错

ALTER TABLEGROUP tg_1 ADD fbs1,fbs2;--多个分区表加入表组的方法。不报错

ALTER TABLE fbs3 TABLEGROUP = tg_1;--分区键名称不一样，但是结构内容一样加上表组。不报错

ALTER TABLE fbs5 TABLEGROUP = tg_1;--分区键名称不一样，结构不一样。会报错， table and tablegroup use different partition options not allowed

```
{: .tips }
> 建表可以没有默认分区。没有默认分区的时候可以添加新的分区，有默认分区的时候无法添加新的分区。

## 配置查询

```sql
show global variables like '%timeout%'
```
|variable_name|value|
|-------------|-----|
|connect_timeout  | 10 |
|interactive_timeout |2880 |
|net_read_timeout   |30 |
|net_write_timeout  |60 |
|ob_pl_block_timeout  |3216672000000000 |
|ob_qeury_timeout      |100000000000 |
|ob_trx_idle_timeout   |120000000000 |
|ob_trx_lock_timeout   | -1 |
|ob_trx_timeout   |100000000000 |
|wait_timeout   |86400 |

```sql
set global ob_query_timeout = 100000000000 ;
set global ob_trx_timeout = 100000000000 ;
set global ob_trx_idle_timeout = 1200000000000 ;

```

## 查视图

```sql
select * from v$version;

select * from gv$sql_audit;
```
QUERY_SQL 实际执行的SQL语句
PLAN_TYPE 执行计划类型：
- 1：本地执行计划（Local）
- 2：远程执行计划（Remote）
- 3：分布式执行计划（Distribute）

[gv$sql_audit视图字段说明](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356239)



## 快速生成DDL

快速生成CREATE-TABLE-DDL语句,查DBA视图，如果要查USER视图全局替换DBA_为USER_即可。

```sql
SELECT 'CREATE TABLE ' || T.TABLE_NAME || ' (' FROM DBA_TABLES T WHERE T.TABLE_NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' 
UNION ALL 
SELECT C.COLUMN_NAME || ' ' || C.DATA_TYPE || ' '|| C.NULLABLE || ' DEFAULT ' || C.DATA_DEFAULT || ',' FROM ( 
SELECT TC.TABLE_NAME, TC.COLUMN_NAME, CASE WHEN TC.DATA_TYPE = 'DATE' THEN TC.DATA_TYPE WHEN TC.DATA_TYPE = 'NUMBER' THEN ( CASE WHEN TC.DATA_PRECISION IS NOT NULL THEN TC.DATA_TYPE || '(' || TC.DATA_PRECISION || ',' || TC.DATA_SCALE || ')' ELSE TC.DATA_TYPE END ) WHEN TC.CHARACTER_SET_NAME IS NOT NULL THEN TC.DATA_TYPE || '(' || TC.CHAR_LENGTH || ')' ELSE TC.DATA_TYPE || '(' || TC.DATA_LENGTH || ')' END DATA_TYPE, CASE TC.NULLABLE WHEN 'N' THEN ' NOT NULL ' END NULLABLE, TC.DATA_DEFAULT FROM DBA_TAB_COLUMNS TC 
WHERE TC.TABLE_NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' ORDER BY TC.TABLE_NAME, TC.COLUMN_ID ) C 
UNION ALL 
SELECT 'CONSTRAINT ' || P.CONSTRAINT_NAME || ' PRIMARY KEY (' || P.COLUMN_NAME || ')' FROM ( 
SELECT CU.TABLE_NAME, CU.CONSTRAINT_NAME , LISTAGG(CU.COLUMN_NAME, ',') WITHIN GROUP (ORDER BY CU.TABLE_NAME,CU.COLUMN_NAME) AS COLUMN_NAME 
FROM DBA_CONS_COLUMNS CU INNER JOIN DBA_CONSTRAINTS AU ON CU.CONSTRAINT_NAME = AU.CONSTRAINT_NAME WHERE CU.OWNER='AMS' AND AU.CONSTRAINT_TYPE = 'P' AND CU.TABLE_NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' GROUP BY CU.TABLE_NAME,CU.CONSTRAINT_NAME ) P 
UNION ALL 
SELECT ')' FROM DUAL 
UNION ALL 
SELECT 'PARTITION BY LIST (' || PK.COLUMN_NAME ||') ' FROM ( 
SELECT PK.NAME, PK.COLUMN_NAME, PK.OBJECT_TYPE, PK.COLUMN_POSITION FROM DBA_PART_KEY_COLUMNS PK WHERE PK.NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' ) PK 
UNION ALL 
SELECT CASE P.PARTITION_NAME WHEN 'SUBCOMPANY_0' THEN '(PARTITION '|| P.PARTITION_NAME || ' VALUES ('|| P.HIGH_VALUE||'),' WHEN 'SUBCOMPANY_WU' THEN 'PARTITION '|| P.PARTITION_NAME || ' VALUES ('|| P.HIGH_VALUE||'));' ELSE 'PARTITION '|| P.PARTITION_NAME || ' VALUES ('|| P.HIGH_VALUE||')' END FROM ( SELECT PT.TABLE_NAME, PT.PARTITION_NAME, PT.HIGH_VALUE, PT.PARTITION_POSITION FROM DBA_TAB_PARTITIONS PT WHERE PT.TABLE_NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' ORDER BY PT.PARTITION_POSITION ) P 
UNION ALL 
SELECT 'COMMENT ON TABLE ' || T.TABLE_NAME || ' IS '''||T.COMMENTS||''';' FROM DBA_TAB_COMMENTS T WHERE T.TABLE_NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' 
UNION ALL 
SELECT 'COMMENT ON COLUMN ' || CC.TABLE_NAME ||'.'|| CC.COLUMN_NAME || ' IS '''||CC.COMMENTS||''';' FROM ( SELECT T.TABLE_NAME , T.COLUMN_NAME, C.COMMENTS 
FROM DBA_TAB_COLUMNS T INNER JOIN DBA_COL_COMMENTS C ON T.TABLE_NAME = C.TABLE_NAME AND T.COLUMN_NAME=C.COLUMN_NAME WHERE T.TABLE_NAME = 'AMS_ACCOUNTIMPDATA_DETAIL_TD' ORDER BY T.COLUMN_ID )CC
```

## OB常见兼容问题

