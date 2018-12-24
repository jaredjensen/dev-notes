# Back up and ZIP SQL Server Databases

Use [7-Zip](https://www.7-zip.org/) to create ZIP files of SQL database backups, and then cull older backups.

## Enable xp_cmdshell

```sql
EXEC sp_configure 'show advanced options', 1
GO
RECONFIGURE
GO
EXEC sp_configure 'xp_cmdshell', 1
GO
RECONFIGURE
GO
```

## Create the SQL Backup Script

This will back up all online databases, compress them into a file named YYYYMMDD.zip, and delete old backups.

```sql
-- Configuration
-- USE "for %I in (.) do echo %~sI" to get DOS 8.1 name
DECLARE @CFG_ZIPEXE_PATH NVARCHAR(256); SET @CFG_ZIPEXE_PATH = 'C:\jobs\DATABA~1\7za.exe'
DECLARE @CFG_BACKUP_PATH NVARCHAR(256); SET @CFG_BACKUP_PATH = 'C:\jobs\DATABA~1\Backups'
DECLARE @CFG_DAYS_DELETE INT; SET @CFG_DAYS_DELETE = 3

DECLARE @Today DATETIME
DECLARE @TodayName CHAR(8)
SET @Today = GETDATE()
SET @TodayName = CONVERT(CHAR(8), @Today, 112)

DECLARE @id INT
DECLARE @name VARCHAR(50)
DECLARE @path VARCHAR(256)
DECLARE @cmd VARCHAR(256)

-- Create temporary directory
DECLARE @TempDir VARCHAR(256)
SET @TempDir = @CFG_BACKUP_PATH + '\' + CONVERT(VARCHAR(256), NEWID())
SET @cmd = 'md ' + @TempDir
EXEC xp_cmdshell @cmd --, no_output

-- Create list of current databases, only 'ONLINE' databases to be backup
DECLARE @dbList TABLE
    (
      dbno INT IDENTITY,
      dbname NVARCHAR(256)
    )
INSERT  INTO @dbList (dbname)
        SELECT  name
        FROM    master.dbo.sysdatabases
        WHERE   name NOT IN ('tempdb', 'master', 'model', 'msdb')
                AND DATABASEPROPERTYEX(name, 'Status') = 'ONLINE'

-- Back up databases individually
SELECT  @id = dbno,
        @name = dbname
FROM    @dbList
WHERE   dbno = 1
WHILE @@ROWCOUNT = 1
    BEGIN
        PRINT N'++ Backup: ' + @name
        SET @path = @TempDir + '\' + @name + '.bak'

        BACKUP DATABASE @name TO DISK = @path

        SELECT  @id = dbno,
                @name = dbname
        FROM    @dbList
        WHERE   dbno = @id + 1
    END

PRINT N'++ Compressing: ' + @TempDir

-- Delete existing ZIP file just in case
SET @cmd = 'del /f /q ' + @CFG_BACKUP_PATH + '\' + @TodayName + '.zip'
EXEC xp_cmdshell @cmd --, no_output

DECLARE @Count INT
DECLARE @StartTime DATETIME
SET @StartTime = GETDATE()
-- Compress, -mx1 = Set Compression Ratio to 1 (very low)
SET @cmd = @CFG_ZIPEXE_PATH + ' a -bd -y -tzip -mx1 ' + @CFG_BACKUP_PATH + '\' + @TodayName + '.zip ' + @TempDir + '\*.bak'
PRINT N'++ Compress: ' + @cmd
EXEC xp_cmdshell @cmd --, no_output

SET @Count = DATEDIFF(second, @StartTime, GETDATE())
PRINT N'++ Compression Time: ' + CONVERT(VARCHAR, @Count) + ' seconds'
SET @Count = DATEDIFF(second, @Today, GETDATE())
PRINT N'++ Total Execution Time: ' + CONVERT(VARCHAR, @Count) + ' seconds'

-- Delete temporary directory
SET @cmd = 'rd /s /q ' + @TempDir
EXEC xp_cmdshell @cmd --, no_output

-- Delete previous backup versions
DECLARE @OlderDateName CHAR(8)
SET @OlderDateName = CONVERT(CHAR(8), @Today - @CFG_DAYS_DELETE, 112)

-- List all ZIP files
CREATE TABLE #delList
    (
      subdirectory VARCHAR(256),
      depth INT,
      [file] BIT
    )
INSERT  INTO #delList
        EXEC xp_dirtree @CFG_BACKUP_PATH, 1, 1
DELETE  #delList
WHERE   RIGHT(subdirectory, 4) <> '.zip'

SELECT  @Count = COUNT(1)
FROM    #delList
PRINT N'++ Number of Backups: ' + CONVERT(NVARCHAR, @Count)

SELECT TOP 1
        @name = subdirectory
FROM    #delList
WHERE   LEN(subdirectory) = 12
        AND RIGHT(subdirectory, 4) = '.zip'
        AND REPLACE(subdirectory, '.zip', '') < @OlderDateName

WHILE ( @@ROWCOUNT = 1 )
    BEGIN
        PRINT N'++ Delete Older Backup: ' + @name
        SET @cmd = 'del /f /q ' + @CFG_BACKUP_PATH + '\' + @name
        EXEC xp_cmdshell @cmd --, no_output

        DELETE  #delList
        WHERE   subdirectory = @name

        SELECT TOP 1
                @name = subdirectory
        FROM    #delList
        WHERE   LEN(subdirectory) = 12
                AND RIGHT(subdirectory, 4) = '.zip'
                AND REPLACE(subdirectory, '.zip', '') < @OlderDateName
    END

DROP TABLE #delList

PRINT N'++ Done.'
PRINT ''
PRINT ''
PRINT ''
```

## Invoke the Backup Script

Create a batch file to invoke the backup script and output to a log file.

```dos
sqlcmd -S .\sqlexpress -i mssql.sql -o mssql.log
```
