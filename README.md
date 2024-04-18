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

## เปลี่ยน Table Owner
```sql
ALTER TABLE my_table OWNER TO new_owner;
```

## เปลี่ยน Sequence Owner
```sql
ALTER SEQUENCE my_sequence OWNER TO new_owner;
```

## เปลี่ยน View Owner
```sql
ALTER VIEW my_view OWNER TO new_owner;
```

## เปลี่ยน Owner ด้วย Script ใน Ubuntu
1. สร้างไฟล์ Script ด้วยคำสั่ง ```nano change_owner_pg.sh``` ใน ubuntu
2. นำคำสั่งด้านล่างใส่ในไฟล์ `change_owner_pg.sh`
```sh
for tbl in `psql -qAt -c "select tablename from pg_tables where schemaname = '$1';" $2` ; do  psql -c "alter table "$1".\"$tbl\" owner to $3" $2 ; done

for tbl in `psql -qAt -c "select tablename from pg_tables where schemaname = '$1';" $2` ; do  psql -c "alter table "$1".\"$tbl\" owner to $3" $2 ; done

for tbl in `psql -qAt -c "select sequence_name from information_schema.sequences where sequence_schema = '$1';" $2` ; do  psql -c "alter sequence "$1".\"$tbl\" owner to $3" $2 ; done

for tbl in `psql -qAt -c "select table_name from information_schema.views where table_schema = '$1';" $2` ; do  psql -c "alter view "$1".\"$tbl\" owner to $3" $2 ; done
```
3. จากนั้น run ด้วยคำสั่ง `sh change_owner_pg.sh "ชื่อ schema" "ชื่อ Database" "ชื่อ New Owner"`

