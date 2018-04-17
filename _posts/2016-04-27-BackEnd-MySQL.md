---
layout: post
title: MySQL 中那些总是记不住的语句
tags: [Back-End]
---

**摘要：**MySQL 中有些语句也总是记不住。就比如说，修改某个字段的名称和类型。这种操作的频率非常低，偶尔修改一次就有可能永远都不能变动了。其实这些语句非常简单，但是就是记不住。所以还是把常用的一些语句先记下来，以备未来之需。
{:.message}

## 增加字段

```sql
alter table <表名> add <字段名> <字段类型> not null default <默认值> auto_increment primary key;
```

## 删除字段

```sql
alter table <表名> drop <字段名>;
```

## 修改字段

```sql
-- 修改字段名称
alter table <表名> change <字段名> <新的字段名> <新的字段类型>;
-- 修改字段类型
alter table <表名> modify <字段名> <字段类型> not null default <默认值> after <字段名>;
```

## 创建视图

```sql
create view <视图名称> as <查询语句>;
```