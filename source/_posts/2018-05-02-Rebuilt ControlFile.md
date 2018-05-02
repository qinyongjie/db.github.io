---
layout:     post
title:      Rebuilt Controlfile
date: 2018-05-02 10:00:00 +0800
keywords:
categories:
tags:
    - note
    - reading
---
This way is applicable software version that is Oracle Database - Enterprise Edition - Version 9.0.1.0 and later

1、需要重建控制文件的几个场景（circumstances）

1.1 控制文件当前所有副本都已经丢失或者损坏
1.2 当在还原一个控制文件遭到丢失或损坏的备份
1.3 需要更改控制文件中的硬限制数据库参数
1.4 当数据库移动到另一台服务器，并且文件位于不同的位置

2、重建控制文件的一般步骤
2.1 数据库实例在MOUNT或者OPEN阶段，生成一份控制文件的转储（一份ascii dump文件）

```sql
SQL> ALTER DATABASE BACKUP CONTROLFILE TO TRACE;
```

2.2 定位trace文件存放路径的两种方法

[1]使用user_dump_dest参数显示
a.显示路径

```sql
SQL> show parameter user_dump_dest 

NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
user_dump_dest                       string      /oravl01/oracle/diag/rdbms/zldb4/zldb4/trace
```

b.找到trace文件
进入到参数显示出来的路径后，使用命令：ls -lrt 按照日期/时间显示出最新生成的跟踪文件

```shell
# cd /oravl01/oracle/diag/rdbms/zldb4/zldb4/trace
# ls -lrt
...
-rw-r-----    1 orauser  asmadmin       9165 Jan 27 17:55 zldb4_ora_15925498.trc
```



[2]使用ORADEBUG命令输出生成trace文件的名称（全路径）                                             
```sql
SQL> oradebug setmypid;         ---跟踪当前会话
Statement processed.
SQL> oradebug tracefile_name;   ---查看trace文件名及位置
/oravl01/oracle/diag/rdbms/zldb4/zldb4/trace/zldb4_ora_15925498.trc
```


2.3从跟踪文件里找到重建控制文件的命令，并编辑成脚本文件，方便执行（createcon.sql）

```sql
CREATE CONTROLFILE REUSE DATABASE "ZLDB4" NORESETLOGS  ARCHIVELOG
    MAXLOGFILES 16
    MAXLOGMEMBERS 3
    MAXDATAFILES 100
    MAXINSTANCES 8
    MAXLOGHISTORY 584
LOGFILE
  GROUP 1 '+ZLDB4_DATA1/zldb4/onlinelog/group_1.257.923458569'  SIZE 1024M BLOCKSIZE 512,
  GROUP 2 '+ZLDB4_DATA1/zldb4/onlinelog/group_2.258.923458571'  SIZE 1024M BLOCKSIZE 512,
  GROUP 3 '+ZLDB4_DATA1/zldb4/onlinelog/group_3.259.923458575'  SIZE 1024M BLOCKSIZE 512
-- STANDBY LOGFILE
DATAFILE
  '+ZLDB4_DATA1/zldb4/datafile/system.260.923458579',
  '+ZLDB4_DATA1/zldb4/datafile/sysaux.261.923458581',
  '+ZLDB4_DATA1/zldb4/datafile/undotbs1.262.923458583',
  '+ZLDB4_DATA1/zldb4/datafile/users.264.923458593',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_data1.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_index1.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_param1.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_data2.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_data3.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_data4.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_data5.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/ts_zhiling_data6.dbf',
  '+ZLDB4_DATA1/zldb4/datafile/test_data.275.965471119',
  '+ZLDB4_DATA1/zldb4/datafile/test_data_20170116',
  '+ZLDB4_DATA1/zldb4/datafile/big_data.280.965566361',
  '+ZLDB4_DATA1/zldb4/datafile/small_data.281.965566743',
  '+ZLDB4_DATA1/zldb4/datafile/test_data.282.966450905'
CHARACTER SET ZHS16GBK
;
```

2.4关闭实例，并删除数据库控制文件，模拟控制文件缺失环境

```sql
SQL> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down.

ASMCMD> ls
Current.256.923458569.bak
ASMCMD> rm -f *
```

2.5在控制文件丢失的情况下，尝试打开数据库

```sql
SQL> SHUTDOWN IMMEDIATE
Database closed.
Database dismounted.
ORACLE instance shut down.

SQL> STARTUP
ORACLE instance started.

Total System Global Area 2.1446E+10 bytes
Fixed Size                  2255824 bytes
Variable Size            3087008816 bytes
Database Buffers         1.8321E+10 bytes
Redo Buffers               35782656 bytes
ORA-00205: error in identifying control file, check alert log for more info
```


2.6重建控制文件

```sql
SQL> @/home/orauser/.scrit/createcon.sql

Control file created.
```

2.7打开数据库
```sql
SQL> ALTER DATABASE OPEN;

Database altered.

SQL> SHOW PARAMETER CON

NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
cluster_interconnects                string
control_file_record_keep_time        integer     7
control_files                        string      +ZLDB4_DATA1/zldb4/controlfile
                                                 /current.256.923458569.bak

SQL> SELECT INSTANCE_NAME,STATUS FROM V$INSTANCE;

INSTANCE_NAME    STATUS
---------------- ------------
zldb4            OPEN
```