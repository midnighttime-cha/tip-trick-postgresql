# SQL และ แก้ปัญหา สำหรับ PostgreSQL

## แก้ๆข Password
```
ALTER USER [Your Username] WITH ENCRYPTED PASSWORD '[New Password]';
```

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

## Grant สิทธิ์
```sql
GRANT USAGE ON SCHEMA "public", schema_1, schema_2 TO new_owner;
GRANT SELECT ON ALL TABLES IN SCHEMA "public", schema_1, schema_2 TO new_owner;
GRANT SELECT ON ALL SEQUENCES IN SCHEMA "public", schema_1, schema_2 TO new_owner ;
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA "public", schema_1, schema_2 TO new_owner ;
GRANT ALL ON ALL TABLES IN SCHEMA "public", schema_1, schema_2 TO new_owner ;
GRANT ALL ON ALL SEQUENCES IN SCHEMA "public", schema_1, schema_2 TO new_owner ;
GRANT ALL ON ALL FUNCTIONS IN SCHEMA "public", schema_1, schema_2 TO new_owner ;
GRANT ALL ON SCHEMA "public", schema_1, schema_2 TO new_owner;
```

## Enable GIS Extension
```sql
CREATE EXTENSION postgis;
```

## Dump/Restore Database
```
pg_dump -h localhost -p 5432 -U [Username] -f backup_15-05-2024.dump [Database Name] -W
pg_restore -d [Database Name] > backup_15-05-2024.dump
```

## ตั้งค่า Error: sorry, too many clients already.
```
nano /etc/postgresql/[POSTGRESQL VERSION]/main/postgresql.conf
```
แก้ไขตามนี้
```
max_connections = 300
shared_buffers = 80MB
```
และไฟล์ sysctl.conf
```
nano /etc/sysctl.conf
```
เพิ่มคำสั่งต่อไปนี้ลงไป
```
kernel.shmmax=100663296
```


## ตั้งค่า Rotage log
เข้าไปแก้ไขในไฟล์ `postgresql.conf`
```
nano /etc/postgresql/[POSTGRESQL VERSION]/main/postgresql.conf
```
ตั้งค่าตามนี้
```
logging_collector = on
log_directory = '/var/log/postgresql'
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_rotation_age = 1d
log_rotation_size = 10MB
```
