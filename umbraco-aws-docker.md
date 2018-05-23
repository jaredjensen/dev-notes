# Load-Balanced Umbraco on AWS in Docker

## Overview

It's not possible to load-balance the Umbraco admin, so you need to split your Umbraco app into a single admin instance and multiple rendering instances.  Our final configuration will consist of three Docker containers:

* Umbraco admin
* Umbraco rendering
* SQL Server

Install SQL Express 2017 image.
```
docker pull microsoft/mssql-server-windows:2017-latest
```


