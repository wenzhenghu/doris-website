---
{
    "title": "用户配置项",
    "language": "zh-CN"
}
---

# User 配置项

该文档主要介绍了 User 级别的相关配置项。User 级别的配置生效范围为单个用户。每个用户都可以设置自己的 User property。相互不影响。

## 查看配置项

FE 启动后，在 MySQL 客户端，通过下面命令查看 User 的配置项：

`SHOW PROPERTY [FOR user] [LIKE key pattern]`

具体语法可通过命令：`help show property;` 查询。

## 设置配置项

FE 启动后，在MySQL 客户端，通过下面命令修改 User 的配置项：

`SET PROPERTY [FOR 'user'] 'key' = 'value' [, 'key' = 'value']`

具体语法可通过命令：`help set property;` 查询。

User 级别的配置项只会对指定用户生效，并不会影响其他用户的配置。

## 应用举例

1. 修改用户 Billie 的 `max_user_connections`

    通过 `SHOW PROPERTY FOR 'Billie' LIKE '%max_user_connections%';` 查看 Billie 用户当前的最大链接数为 100。

    通过 `SET PROPERTY FOR 'Billie' 'max_user_connections' = '200';` 修改 Billie 用户的当前最大连接数到 200。

## 配置项列表

### max_user_connections

    用户最大的连接数，默认值为100。一般情况不需要更改该参数，除非查询的并发数超过了默认值。

### max_query_instances

    用户同一时间点可使用的instance个数, 默认是-1，小于等于0将会使用配置default_max_query_instances.

### resource

### quota

### default_load_cluster

### load_cluster
