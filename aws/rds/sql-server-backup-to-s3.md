# Create SQL Server Backup to S3

First, enable backups:

1. AWS Console > Services > RDS > Option groups > Create Group
2. Enter SQL version information and create the group
3. Select new group and choose **Add Option**
4. Select `SQLSERVER_BACKUP_RESTORE`, create a new IAM role, select/create the S3 bucket, and apply immediately

Next, take the database [offline](sql-server-online-offline.md).

Now connect to the RDS server and create the backup:

```sql
-- Perform the backup (unencrypted)
exec msdb.dbo.rds_backup_database
  @source_db_name='<database_name>',
  @s3_arn_to_backup_to='arn:aws:s3:::<bucket_name/file_name_and_extension>',
  @overwrite_S3_backup_file=1;

-- Check backup status; it's complete when lifecycle is SUCCESS
exec msdb.dbo.rds_task_status @db_name='<database_name>';
```

## References

- [Importing and Exporting SQL Server Databases](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html)
- [How do I perform native backups of an RDS SQL Server DB instance?](https://aws.amazon.com/premiumsupport/knowledge-center/native-backup-rds-sql-server/)
