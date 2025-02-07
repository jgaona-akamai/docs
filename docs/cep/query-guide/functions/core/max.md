---
title: max (Aggregate Function)
---

Returns the maximum value for all the events.

## Syntax

```sql
    <INT|LONG|DOUBLE|FLOAT> max(<INT|LONG|DOUBLE|FLOAT> arg)
```

## Query Parameters

| Name | Description                                                    | Default Value | Possible Data Types   | Optional | Dynamic |
|------|----------------------------------------------------------------|---------------|-----------------------|----------|---------|
| arg  | The value that needs to be compared to find the maximum value. |               | INT LONG DOUBLE FLOAT | No       | Yes     |

## Example

```sql
    INSERT INTO barStream
    SELECT max(temp) AS maxTemp
    FROM fooStream WINDOW TUMBLING_TIME(10 sec);
```

`max(temp)` returns the maximum temp value recorded for all the events based on their arrival and expiration.
