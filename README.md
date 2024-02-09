# SQL สำหรับ PostgreSQL

## สร้าง Database
```
CREATE DATABASE [Your Database];
```

## สร้าง User
```
CREATE USER [Your Username] WITH ENCRYPTED PASSWORD '[Your Password]';
GRANT ALL PRIVILEGES ON DATABASE [Your Database] TO [Your Username];
```

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

## เช็คซ้ำ Having
```sql
SELECT username, email, COUNT(*)
FROM users
GROUP BY username, email
HAVING COUNT(*) > 1
```
