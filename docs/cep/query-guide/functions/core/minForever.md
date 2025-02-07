---
title: minForever (Aggregate Function)
---

This is the attribute aggregator to store the minimum value for a given attribute throughout the lifetime of the query regardless of any windows.

## Syntax

```sql
<INT|LONG|DOUBLE|FLOAT> minForever(<INT|LONG|DOUBLE|FLOAT> arg)
```

## Query Parameters

| Name | Description                                                    | Default Value | Possible Data Types   | Optional | Dynamic |
|------|----------------------------------------------------------------|---------------|-----------------------|----------|---------|
| arg  | The value that needs to be compared to find the minimum value. |               | INT LONG DOUBLE FLOAT | No       | Yes     |

## Example

```sql
INSERT INTO outputStream
SELECT minForever(temp) AS max
FROM inputStream;
```

`minForever(temp)` returns the minimum temp value recorded for all the events throughout the lifetime of the query.
