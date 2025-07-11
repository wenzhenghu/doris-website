---
{
    "title": "Variable",
    "language": "en"
}
---

# Variable

This document focuses on currently supported variables.

Variables in Doris refer to variable settings in MySQL. However, some of the variables are only used to be compatible with some MySQL client protocols, and do not produce their actual meaning in the MySQL database.

## Variable setting and viewing

### View

All or specified variables can be viewed via `SHOW VARIABLES [LIKE 'xxx'];`. Such as:

```
SHOW VARIABLES;
SHOW VARIABLES LIKE '%time_zone%';
```

### Settings

Note that before version 1.1, after the setting takes effect globally, the setting value will be inherited in subsequent new session connections, but the value in the current session will remain unchanged.
After version 1.1 (inclusive), after the setting takes effect globally, the setting value will be used in subsequent new session connections, and the value in the current session will also change.

For session-only, set by the `SET var_name=xxx;` statement. Such as:

```
SET exec_mem_limit = 137438953472;
SET forward_to_master = true;
SET time_zone = "Asia/Shanghai";
```

For global-level, set by `SET GLOBAL var_name=xxx;`. Such as:

```
SET GLOBAL exec_mem_limit = 137438953472
```

> Note 1: Only ADMIN users can set variable at global-level.

Variables that support both session-level and global-level setting include:

* `time_zone`
* `wait_timeout`
* `sql_mode`
* `enable_profile`
* `query_timeout`
* `insert_timeout` 
* `exec_mem_limit`
* `batch_size`
* `parallel_fragment_exec_instance_num`
* `parallel_exchange_instance_num`
* `allow_partition_column_nullable`
* `insert_visible_timeout_ms`
* `enable_fold_constant_by_be`

Variables that support only global-level setting include:

* `default_rowset_type`
* `default_password_lifetime`
* `password_history`
* `validate_password_policy`

At the same time, variable settings also support constant expressions. Such as:

```
SET exec_mem_limit = 10 * 1024 * 1024 * 1024;
SET forward_to_master = concat('tr', 'u', 'e');
```

### Set variables in the query statement

In some scenarios, we may need to set variables specifically for certain queries.
The SET_VAR hint sets the session value of a system variable temporarily (for the duration of a single statement). Examples:

```
SELECT /*+ SET_VAR(exec_mem_limit = 8589934592) */ name FROM people ORDER BY name;
SELECT /*+ SET_VAR(query_timeout = 1, enable_partition_cache=true) */ sleep(3);
```

Note that the comment must start with /*+ and can only follow the SELECT.

## Supported variables

> Note:
>
> The following content is automatically generated by `docs/generate-config-and-variable-doc.sh`.
>
> To modify, please modify `fe/fe-core/src/main/java/org/apache/doris/qe/SessionVariable.java` and `fe/fe-core/src/main/java/org/apache/ Description information in doris/qe/GlobalVariable.java`.

<--DOC_PLACEHOLDER-->

