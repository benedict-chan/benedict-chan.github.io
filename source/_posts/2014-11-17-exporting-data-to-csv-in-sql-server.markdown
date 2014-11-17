---
layout: post
title: "Exporting data to CSV in SQL Server using bcp"
date: 2014-11-17 16:13:22 +1100
comments: false
categories: ["sql", "data"]
description:
keywords: "extract data csv sql server bcp "
---

There are several ways to export data in SQL Server, in this post, I am using `bcp` with `xp_cmdshell`.

We will first create a simple select statement to test whether we can export our data, then we will use a stored procedure to handle complex queries. Finally we will try to add variables to our script so that we can make this export script to be run in SQL Server Agent as a scheduled task.


### Test the export with a simple select statement ###

{% raw %}
``` sql
exec master..xp_cmdshell 'bcp "select * from DataSeries" queryout "C:\Export\dataseries.csv" -c -t"," -r"\n" -T'
```
{% endraw %}

However, you will receive an error of: 
[SQL Server]Invalid object name 'Table'.

So, in order to run this simple query, we actually need to tell SQL Server the full name of our table schema, which should be:

{% raw %}
``` sql
exec master..xp_cmdshell 'bcp "select * from [db_name].[dbo].[DataSeries]" queryout "C:\Export\dataseries.csv" -c -t"," -r"\n" -T'
```
{% endraw %}


### Complex Query, use stored procedure ###

<!-- more -->

For a complex query, we should store our query in a Stored Procedure, for example if we have a stored procedure name `ExportDataSeries` with two `DateTime` input parameters, we can change our script to:

{% raw %}
``` sql
exec master..xp_cmdshell 'bcp "exec [db_name].[dbo].[ExportDataSeries] ''2014-11-17'', ''2014-11-24'' " queryout "C:\Export\dataseries.csv" -c -t"," -r"\n" -T'

```
{% endraw %}


### Refactor and apply variables for the query ###

Now that we have worked out our stored procedure, let's try to refactor our script by injecting some variables, such that we can use this in SQL Server Agent for repeated task. In this case, let's assume this is a daily schedule which will export data to a CSV file everyday.

The final script looks like this:

{% raw %}
``` sql
-- DECLARE variables for the DateTime parameters
DECLARE @DayStart DATETIME
DECLARE @DayEnd DATETIME
DECLARE @DayStartArg NVARCHAR(50);
DECLARE @DayEndArg NVARCHAR(50);

SET @DayStart =  CAST(CONVERT(VARCHAR(10), GETDATE(), 110) + ' 00:00:00' AS DATETIME)
SET @DayEnd =  CAST(CONVERT(VARCHAR(10), GETDATE(), 110) + ' 23:59:59' AS DATETIME)
SET @DayStartArg = CONVERT(NVARCHAR(20), @DayStart,126);
SET @DayEndArg = CONVERT(NVARCHAR(20), @DayEnd,126);

-- DECLARE variables for export location
DECLARE @ExportFolderName NVARCHAR(90);
DECLARE @ExportFileName NVARCHAR(90);

SET @ExportFolderName = 'C:\Export\'; 
SET @ExportFileName = @ExportFolderName + 'ExportDataSeries_' + RTRIM(CONVERT(NVARCHAR(20), @DayStart,112)) + '.csv';

-- DECLARE variables for the execution scripts
DECLARE @SqlStatement varchar(8000)
DECLARE @BcpStatement varchar(8000)

SET @SqlStatement = 'exec [db_name].[dbo].[ExportDataSeries]  ''' + @DayStartArg + ''', ''' + @DayEndArg + ''''
SET @BcpStatement = 'bcp "' + @SqlStatement + '" queryout "' + @ExportFileName + '" -c -t"," -r"\n" -T'

exec master..xp_cmdshell @BcpStatement


```
{% endraw %}

For your interest, variable `@BcpStatement` looks like this:

{% raw %}
``` sql
bcp "exec [db_name].[dbo].[ExportDataSeries]  '2014-11-17T00:00:00', '2014-11-17T23:59:59'" queryout "C:\Export\ExportDataSeries_20141117.csv" -c -t"," -r"\n" -T

```
{% endraw %}
