---
{
    "title": "ALTER-SYSTEM-MODIFY-BACKEND",
    "language": "en"
}

---

## ALTER-SYSTEM-MODIFY-BACKEND

### Name

ALTER SYSTEM MKDIFY BACKEND

### Description

Modify BE node properties (administrator only!)

grammar:

```sql
ALTER SYSTEM MODIFY BACKEND "host:heartbeat_port" SET ("key" = "value"[, ...]);
```

  illustrate:

1. host can be a hostname or an ip address
2. heartbeat_port is the heartbeat port of the node
3. Modify BE node properties The following properties are currently supported:

- tag.xxxx: resource tag
- disable_query: query disable attribute
- disable_load: import disable attribute

Note:
1. A backend can be set multi resource tags. But must contain "tag.location" type.

### Example

1. Modify the resource tag of BE

    ```sql
    ALTER SYSTEM MODIFY BACKEND "host1:heartbeat_port" SET ("tag.location" = "group_a");
    ALTER SYSTEM MODIFY BACKEND "host1:heartbeat_port" SET ("tag.location" = "group_a", "tag.compute" = "c1");
    ```

2. Modify the query disable property of BE

    ```sql
    ALTER SYSTEM MODIFY BACKEND "host1:heartbeat_port" SET ("disable_query" = "true");
    ```

3. Modify the import disable property of BE

    ```sql
    ALTER SYSTEM MODIFY BACKEND "host1:heartbeat_port" SET ("disable_load" = "true");
    ```

### Keywords

    ALTER, SYSTEM, ADD, BACKEND, ALTER SYSTEM

### Best Practice

