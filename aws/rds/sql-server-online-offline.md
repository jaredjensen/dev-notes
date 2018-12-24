# Set SQL Server Database Online/Offline

```sql
USE master

-- Take offline
ALTER DATABASE <db_name> SET OFFLINE

-- Bring online
EXEC rdsadmin.dbo.rds_set_database_online <db_name>
```
