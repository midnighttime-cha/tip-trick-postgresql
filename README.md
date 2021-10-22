# SQL สำหรับ PostgreSQL

## ตรวจสอบ Session และ connection
```sql
SELECT * FROM pg_stat_activity;
```
หรือ
```sql
select pid as process_id, 
       usename as username, 
       datname as database_name, 
       client_addr as client_address, 
       application_name,
       backend_start,
       state,
       state_change
from pg_stat_activity;
```
