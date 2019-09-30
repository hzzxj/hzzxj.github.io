---
title: postgresql发生锁表
date: 2019-09-30 10:13:38
tags: 
- postgresql
categories:
- postgresql
---

### 查询相关表当前的活动进程：
```sql
SELECT * FROM pg_stat_activity where query ~ 'table_name';
```
### 解锁
```sql
SELECT pg_terminate_backend(pid) FROM pg_stat_activity where query ~ 'table_name' and pid <> pg_backend_pid();
```