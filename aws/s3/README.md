# S3

## Sync Folder to Bucket

```dos
rem If running from a Scheduled Task, set the task profile name before using aws
set USERPROFILE=C:\Users\Administrator

rem Sync the local folder to S3
aws s3 sync <local folder path> s3://<bucket>/<path> --delete

rem Example
rem aws s3 sync c:\backups s3://mybucket/backups --delete
```
