---
layout: default
title: ORACLE Jobs
nav_order: 21
parent: Database
---

# ORACLE Jobs
{: .no_toc }

## TABLE of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## oracle 

Oracle 在线学习：https://livesql.oracle.com


### Oracle DBA_JOBS


【Oracle】dba_jobs字段说明

```
 1 字段（列）               数据类型              描述 
 2 JOB                    NUMBER               任务的唯一标示号 
 3 LOG_USER               VARCHAR2(30)         提交任务的用户 
 4 PRIV_USER              VARCHAR2(30)         赋予任务权限的用户 
 5 SCHEMA_USER            VARCHAR2(30)         对任务作语法分析的用户模式 
 6 LAST_DATE              DATE                 最后一次成功运行任务的时间 
 7 LAST_SEC               VARCHAR2(8)          如HH24:MM:SS格式的last_date日期的小时，分钟和秒 
 8 THIS_DATE              DATE                 正在运行任务的开始时间，如果没有运行任务则为null 
 9 THIS_SEC               VARCHAR2(8)          如HH24:MM:SS格式的this_date日期的小时，分钟和秒 
10 NEXT_DATE              DATE                 下一次定时运行任务的时间 
11 NEXT_SEC               VARCHAR2(8)          如HH24:MM:SS格式的next_date日期的小时，分钟和秒 
12 TOTAL_TIME             NUMBER               该任务运行所需要的总时间，单位为秒 
13 BROKEN                 VARCHAR2(1)          标志参数，Y标示任务中断，以后不会运行 
14 INTERVAL               VARCHAR2(200)        用于计算下一运行时间的表达式 
15 FAILURES               NUMBER               任务运行连续没有成功的次数 
16 WHAT                   VARCHAR2(2000)       执行任务的PL/SQL块 
17 CURRENT_SESSION_LABEL  RAW MLSLABEL         该任务的信任Oracle会话符 
18 CLEARANCE_HI           RAW MLSLABEL         该任务可信任的Oracle最大间隙 
19 CLEARANCE_LO           RAW MLSLABEL         该任务可信任的Oracle最小间隙 
20 NLS_ENV                VARCHAR2(2000)       任务运行的NLS会话设置 
21 MISC_ENV               RAW(32)              任务运行的其他一些会话参数
```
相关视图还有all_jobs、user_jobs、dba_jobs_running-正在运行的job

执行时间说明

（1）每分钟执行

整点执行（也就是00秒）：
> Interval => TRUNC(sysdate,‘mi’) + 1/ (24*60)

当前时间每过一分钟执行一次：

>Interval => sysdate+1/(24*60)

（2）每小时执行一次

当前时间每过一小时执行一次：
> Interval => ‘SYSDATE + 1/24’
每过一个小时整点执行（00分00秒）：

> interval=>trunc(sysdate,‘hh24’)+1/24

（3）每天的凌晨1点执行

> Interval => TRUNC(sysdate) + 1 +1/ (24)

（4）每周二凌晨1点执行

> Interval => TRUNC(NEXT_DAY(sysdate,2))+1/24

（5）每月1日凌晨1点执行

> Interval =>TRUNC(LAST_DAY(SYSDATE))+1+1/24

（6）每季度的第一天凌晨1点执行
> Interval => TRUNC(ADD_MONTHS(SYSDATE,3),‘Q’) + 1/24

（7）每年7月1日和1月1日凌晨1点
> Interval => ADD_MONTHS(trunc(sysdate,‘yyyy’),6)+1/24

（8）每年1月1日凌晨1点执行
> Interval =>ADD_MONTHS(trunc(sysdate,‘yyyy’),12)+1/24

使用示例

建表：
```sql
create table test_job(
v_date timestamp
);
```

存储过程：
```sql
create or replace procedure proc_test_job
as
begin
insert into test_job(v_date)values(sysdate);
commit;
end;
/
```

建立job：

```sql
declare
    job1 number;
begin
dbms_job.submit(
        JOB =>job1,
        WHAT=>‘proc_test_job;’,
        interval=>‘sysdate+1/(24*60)’,
        next_date=>sysdate
        );
end;
/
```


手动执行、删除、修改或者停止定时任务

先查询定时任务的唯一标识号（也就是job字段）,根据what字段进行查询：

```sql
select job from use_jobs where what like '%proc_test_job%';
```

得到唯一标识后，如果想执行，则执行一下sql：
```sql
begin
dbms_job.run(123);
end;
```
> 123为上一步查询到的唯一标识

得到唯一标识后，如果想删除，则执行一下sql：
```sql
begin
dbms_job.remove(123);
end;
```
>123为上一步查询到的唯一标识

得到唯一标识后，如果想修改定时任务，则执行一下sql：
```sql
begin
dbms_job.change(
JOB =>123,
WHAT=>‘proc_test_job;’,
interval=>‘sysdate+2/(24*60)’,
next_date=>sysdate
);
end;
```
>123为上一步查询到的唯一标识，根据它指定对应的job


得到唯一标识后，如果想修改定时任务，则执行一下sql：

```sql
begin
dbms_job.broken(
job=>123,
broken=>true
);
end;
```
#123为上一步查询到的唯一标识，**broken为true时是停止，false时启动**

{: .tips }
>注：
>job的并行度由系统参数job_queue_processes控制，如果这个参数为0的时候所有job都不会运行的。我们检查job是否运行可以根据broken参数查看。为Y时说明job已经停了。如果想查看当前正在执行的job可以根据视图dba_jobs_running

设置 job_queue_processes 参数

```sql
ALTER SYSTEM SET job_queue_processes = 10;
```

查看可执行的job数sql

```sql
select * from v$parameter where name = 'job_queue_process';
```

查看可执行的job数（命令窗口执行）
```sql
SQL> show parameter job_queue_process;
NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
job_queue_processes                  integer     10

```

查看当前正在执行的job

```sql
select * from  dba_jobs_running ;
```

### Oracle Schedule

在11g中，Oracle提供了一个新建的Scheduler特性，帮助将作业实现自动化。它还可以帮助你控制资源的利用与并可以将数据库中的作业按优先顺序执行。传统的dbms_jobs的一个限制是它只能调度基于PL/SQL的作业，不能用来调度操作系统的可执行文件或脚本。



dbms_scheduler.create_job

授权

```sql
grant scheduler_admin  to ams
grant manage scheduler to ams
grant  create job  to ams
```

create job系统权限允许做如下工作：

创建作业（job）、进度表（schedule）、程序（program）、链（chain）和事件（event）。

#### 1、使用procedure创建调度任务

```sql
begin

    dbms_scheduler.create_job(job_name            => 'xxxx',
                            job_type            => 'STORED_PROCEDURE',
                            job_action          => 'xxxx',
                            start_date          => to_date('04-08-2018 04:50:15', 'dd-mm-yyyy hh24:mi:ss'),
                            repeat_interval     => 'Freq=Daily;Interval=1;ByHour=4;ByMinute=50;',
                            end_date            => null,
                            job_class           => 'DEFAULT_JOB_CLASS',
                            enabled             => true,
                            auto_drop           => false,
                            comments            => 'xxxxxxx');
end;
```
参数说明：

 -   job_name： 任务名称
 -   job_type：有三种类型，PL/SQL Block、Stored procedure、Executable
 -   job_action：根据job_type的不同，有不同的含义
      -  如果job_type指定的是存储过程，就需要指定存储过程的名字;
      -  如果job_type指定的是PL/SQL块，就需要输入完整的PL/SQL代码;
      -  如果job_type指定的外部程序，就需要输入script的名称或者操作系统的指令名

 -   start_date：开始时间
 -   repeat_interval：运行的时间间隔，上面例子是每天23点运行一次
 -   end_date：到期时间
 -   enabled：创建后自动激活
 -   auto_drop：默认true,即当job执行完毕都到期是否直接删除job

#### 2、常用的写法

```sql
--删除job
 begin
     dbms_scheduler.drop_job('xxxx');
end;

--禁用
 begin
     dbms_scheduler.disable('xxxx');
end;

    --启动
 begin
     dbms_scheduler.enable('xxxx');
 end;


exec dbms_scheduler.enable('demo_job');  --启用jobs  
     
exec dbms_scheduler.disable('demo_job');  --禁用jobs  
     
exec dbms_scheduler.run_job('demo_job');  --执行jobs  
     
exec dbms_scheduler.stop_job('demo_job');  --停止jobs  
     
exec dbms_scheduler.drop_job('demo_job');  --删除jobs 
```

#### 3、常用频率

1.每周5的时候运行，以下3条实现功能一样

    REPEAT_INTERVAL => 'FREQ=DAILY; BYDAY=FRI';

    REPEAT_INTERVAL => 'FREQ=WEEKLY; BYDAY=FRI';

    REPEAT_INTERVAL => 'FREQ=YEARLY; BYDAY=FRI';

2.每隔一周运行一次，仅在周5运行

    REPEAT_INTERVAL => 'FREQ=WEEKLY; INTERVAL=2; BYDAY=FRI’;

3.每月最后一天运行

    REPEAT_INTERVAL => 'FREQ=MONTHLY; BYMONTHDAY=-1'

4.在3月10日运行

    REPEAT_INTERVAL => 'FREQ=YEARLY; BYMONTH=MAR; BYMONTHDAY=10’;

    REPEAT_INTERVAL => 'FREQ=YEARLY; BYDATE=0310';

5.每10隔天运行

    REPEAT_INTERVAL => 'FREQ=DAILY; INTERVAL=10’;

6.每天的下午4、5、6点时运行

    REPEAT_INTERVAL => 'FREQ=DAILY; BYHOUR=16,17,18’;

7.每月29日运行

    REPEAT_INTERVAL => 'FREQ=MONTHLY; BYMONTHDAY=29’;

8.每年的最后一个周5运行

    REPEAT_INTERVAL => 'FREQ=YEARLY; BYDAY=-1FRI’;

9.每隔50个小时运行

    REPEAT_INTERVAL => 'FREQ=HOURLY; INTERVAL=50’;

使用plsql块创建job

```sql
 --创建作业程序(pragram)
begin
     sys.dbms_scheduler.create_program(
     program_name=>'demo_pragram',
     program_type=>'PLSQL_BLOCK',
     program_action=>'BEGIN p_insSysdate;END;',
     enabled=>TRUE,
     comments=>'test pragram');
end;
/

--创建job
begin
     sys.dbms_scheduler.create_job(
     job_name=>'demo_job',
     program_name=>'demo_pragram',
     schedule_name=>'demo_scheduler',
     job_class=>'DEFAULT_JOB_CLASS',
     enabled=>true,
     auto_drop=>true,
     comments=>'test job');
end;
 /

--执行job
exec sys.dbms_scheduler.run_job('demo_job');
```

shell 调度

```bash
vi test.sh 

#!/bin/bash
source /home/oracle/.bash_profile
/bin/date


BEGIN
  dbms_scheduler.create_job(job_name     => 'JOB_TEST.sh',
                            job_type => 'EXECUTABLE',
                            job_action => '/home/oracle/test.sh',
                            enabled      => TRUE,
                            auto_drop => FALSE); --->值为TRUE用于激活JOB 
END;
/
```


相关表的查询
     
````sql

select job_name,state from dba_scheduler_jobs;

select owner,job_name, status,error#,cpu_used,additional_info from dba_scheduler_job_run_details where error#>0;

```