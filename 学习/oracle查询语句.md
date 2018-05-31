``` sql
--查看表空间使用情况
SELECT UPPER(F.TABLESPACE_NAME) "表空间名",
D.TOT_GROOTTE_MB "表空间大小(M)",
D.TOT_GROOTTE_MB - F.TOTAL_BYTES "已使用空间(M)",
TO_CHAR(ROUND((D.TOT_GROOTTE_MB - F.TOTAL_BYTES) / D.TOT_GROOTTE_MB * 100,2),'990.99') "使用比",
F.TOTAL_BYTES "空闲空间(M)",
F.MAX_BYTES "最大块(M)"
FROM (SELECT TABLESPACE_NAME,
ROUND(SUM(BYTES) / (1024 * 1024), 2) TOTAL_BYTES,
ROUND(MAX(BYTES) / (1024 * 1024), 2) MAX_BYTES
FROM SYS.DBA_FREE_SPACE
GROUP BY TABLESPACE_NAME) F,
(SELECT DD.TABLESPACE_NAME,
ROUND(SUM(DD.BYTES) / (1024 * 1024), 2) TOT_GROOTTE_MB
FROM SYS.DBA_DATA_FILES DD
GROUP BY DD.TABLESPACE_NAME) D
WHERE D.TABLESPACE_NAME = F.TABLESPACE_NAME
ORDER BY 4 DESC;

```

--查询表空间使用前10

``` sql
SELECT *
FROM (SELECT SEGMENT_NAME, SUM(BYTES) / 1024 / 1024 MB
FROM DBA_SEGMENTS
WHERE TABLESPACE_NAME = 'SYSTEM'
GROUP BY SEGMENT_NAME ORDER BY 2 DESC)
WHERE ROWNUM <= 10;
```

``` sql
--查询表空间存在的表
select TABLE_NAME,TABLESPACE_NAME
from dba_tables
where TABLESPACE_NAME='SYSTEM' --and table_name = 'IDL_UB1$';
```

``` sql
--查看表空间存在表的使用情况
select t.tablespace_name,t.segment_name, t.segment_type, sum(t.bytes / 1024 / 1024) "占用空间(M)"
from dba_segments t
where t.segment_type='TABLE'
--and t.segment_name = 'IDL_UB1$'
group by OWNER, t.segment_name, t.segment_type, t.tablespace_name;
```
