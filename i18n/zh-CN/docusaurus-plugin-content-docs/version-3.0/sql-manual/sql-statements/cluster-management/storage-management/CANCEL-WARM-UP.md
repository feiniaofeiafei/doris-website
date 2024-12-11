---
{
"title": "CANCEL WARM UP",
"language": "zh-CN"
}
---

<!-- 
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

## 描述

用于在 Doris 终止指定的预热作业。

## 语法

```sql
CANCEL WARM UP JOB WHERE id = <id>;
```

## 必选参数

`<id>`

> 想要终止的预热任务的 id，可以通过命令 `SHOW WARM UP JOB` 查询得到。

## 权限控制

执行此 SQL 命令的用户必须至少具有 ADMIN_PRIV 权限。

## 示例

通过 `SHOW WARM UP JOB` 查询得到当前系统中运行的预热任务：

```sql
SHOW WARM UP JOB
```

其结果为：

```C++
+----------------+---------------+---------+-------+-------------------------+-------------+----------+------------+--------+
| JobId          | ClusterName   | Status  | Type  | CreateTime              | FinishBatch | AllBatch | FinishTime | ErrMsg |
+----------------+---------------+---------+-------+-------------------------+-------------+----------+------------+--------+
| 90290165739458 | CloudCluster1 | RUNNING | TABLE | 2024-11-11 11:11:42.700 | 1           | 3        | NULL       |        |
+----------------+---------------+---------+-------+-------------------------+-------------+----------+------------+--------+
```

然后通过 `CANCEL WARM UP` 进行终止：

```C++
CANCEL WARM UP WHERE id = 90290165739458;
```

若返回以下内容则表明指定 id 对应的预热任务不存在：

```C++
ERROR 1105 (HY000): errCode = 2, detailMessage = job id: 110 does not exist.
```

正确返回后再次 `SHOW WARM UP JOB` 可以看到任务状态从 RUNNING 变更为 CANCELLED：

```C++
+----------------+---------------+-----------+-------+-------------------------+-------------+----------+-------------------------+-------------+
| JobId          | ClusterName   | Status    | Type  | CreateTime              | FinishBatch | AllBatch | FinishTime              | ErrMsg      |
+----------------+---------------+-----------+-------+-------------------------+-------------+----------+-------------------------+-------------+
| 90290165739458 | CloudCluster1 | CANCELLED | TABLE | 2024-11-11 11:11:42.700 | 1           | 3        | 2024-11-11 11:11:43.700 | user cancel |
+----------------+---------------+-----------+-------+-------------------------+-------------+----------+-------------------------+-------------+
```