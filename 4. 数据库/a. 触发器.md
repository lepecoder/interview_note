触发器负责在某个事件发生时自动执行。

触发器是 MySQL 响应以下任意语句而自动执行的一条 MySQL 语句，(或位于BEGIN，END之间的语句)

可以响应 DELETE   INSERT UPDATE

```sql
CREATE TRIGGER newproduct AFTER INSERT ON products
FOR EACH ROW SELECT 'Product added';
```