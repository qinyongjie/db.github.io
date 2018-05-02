---
layout:     post
title:      rebuilt controlfile
date: 2018-05-02 23:07:00 +0800
keywords:   jar
categories:   
	- CS
tags:		
	- jar
	- URL
---

1����Ҫ�ؽ������ļ��ļ���������circumstances��

1.1 �����ļ���ǰ���и������Ѿ���ʧ������
1.2 ���ڻ�ԭһ�������ļ��⵽��ʧ���𻵵ı���
1.3 ��Ҫ���Ŀ����ļ��е�Ӳ�������ݿ����
1.4 �����ݿ��ƶ�����һ̨�������������ļ�λ�ڲ�ͬ��λ��

2���ؽ������ļ���һ�㲽��
2.1 ���ݿ�ʵ����MOUNT����OPEN�׶Σ�����һ�ݿ����ļ���ת����һ��ascii dump�ļ���

```sql
SQL> ALTER DATABASE BACKUP CONTROLFILE TO TRACE;
```

2.2 ��λtrace�ļ����·�������ַ���

[1]ʹ��user_dump_dest������ʾ
a.��ʾ·��

```sql
SQL> show parameter user_dump_dest 

NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
user_dump_dest                       string      /oravl01/oracle/diag/rdbms/zldb4/zldb4/trace
```

b.�ҵ�trace�ļ�
���뵽������ʾ������·����ʹ�����ls -lrt ��������/ʱ����ʾ���������ɵĸ����ļ�

```shell
# cd /oravl01/oracle/diag/rdbms/zldb4/zldb4/trace
# ls -lrt
...
-rw-r-----    1 orauser  asmadmin       9165 Jan 27 17:55 zldb4_ora_15925498.trc
```



[2]ʹ��ORADEBUG�����������trace�ļ������ƣ�ȫ·����                                             
```sql
SQL> oradebug setmypid;         ---���ٵ�ǰ�Ự
Statement processed.
SQL> oradebug tracefile_name;   ---�鿴trace�ļ�����λ��
/oravl01/oracle/diag/rdbms/zldb4/zldb4/trace/zldb4_ora_15925498.trc
```


2.3�Ӹ����ļ����ҵ��ؽ������ļ���������༭�ɽű��ļ�������ִ�У�createcon.sql��

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

2.4�ر�ʵ������ɾ�����ݿ�����ļ���ģ������ļ�ȱʧ����

```sql
SQL> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down.

ASMCMD> ls
Current.256.923458569.bak
ASMCMD> rm -f *
```

2.5�ڿ����ļ���ʧ������£����Դ����ݿ�

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


2.6�ؽ������ļ�

```sql
SQL> @/home/orauser/.scrit/createcon.sql

Control file created.
```

2.7�����ݿ�
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

[Link](http://www.cndba.cn/Aesthetik/article/2596)
