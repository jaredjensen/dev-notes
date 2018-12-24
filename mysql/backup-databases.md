# Backup MySQL Databases

If I ever need this again, I'll parameterize it better and run from an external script.

```bash
ECHO OFF

SET USR=root
SET PWD=<root password>
SET DIR=<path to backup directory, no trailing slash>

REM Get date in YYYYMMDD format
FOR /F "TOKENS=1* DELIMS= " %%A IN ('DATE/T') DO SET CDATE=%%B
FOR /F "TOKENS=1,2 eol=/ DELIMS=/ " %%A IN ('DATE/T') DO SET mm=%%B
FOR /F "TOKENS=1,2 DELIMS=/ eol=/" %%A IN ('echo %CDATE%') DO SET dd=%%B
FOR /F "TOKENS=2,3 DELIMS=/ " %%A IN ('echo %CDATE%') DO SET yyyy=%%B
SET DTE=%yyyy%%mm%%dd%

REM Change the working directory
cd "C:\Program Files\MySQL\MySQL Server 5.6\bin"

REM mysqldump --user=%USR% --password=%PWD% <db_name> > "%DIR%\<db_name>_%DTE%.sql"

REM Clean up files older than 3 days
REM FORFILES /P "%DIR%" /D -3 /M <db_name>_*.sql /C "CMD /C DEL @path" 2>&1 | find /v /i "ERROR: No files found with the specified search criteria."
```
