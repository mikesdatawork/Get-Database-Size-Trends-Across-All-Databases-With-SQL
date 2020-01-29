![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Get Database Size Trends Across All Databases With SQL
**Post Date: July 27, 2015**





## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's some SQL logic that will use the database size before and after backup compression and produce a simple report. This logic was originally created at SQLSkills from Erin Stellato. The original post can be found here: http://www.sqlskills.com/blogs/erin/trending-database-growth-from-backups/ She points out how you can easily trend this data via excel.
I simply modified the logic to include all databases, and to check to see if the database still exists.</p>     


## SQL-Logic
```SQL
use master;
set nocount on
 
declare @get_dbsize_history varchar(max)
 
declare @first_day_of_year varchar(25)
 
set @first_day_of_year = (select dateadd(year, datediff(year, 0, getdate()), 0))
 
set @get_dbsize_history = ''
 
select @get_dbsize_history = @get_dbsize_history +
 
'
 
select
 
''database'' = cast([database_name] as varchar(20))
 
, ''month'' = convert(varchar(7),[backup_start_date],120)
 
, ''backup size gb'' = str(avg([backup_size]/1024/1024/1024),5,2)
 
, ''compressed bu size'' = str(avg([compressed_backup_size]/1024/1024/1024),5,2)
 
, ''compression ratio'' = str(avg([backup_size]/[compressed_backup_size]),5,2)
 
from
 
msdb.dbo.backupset
 
where
 
[database_name] = ''' + name + '''
 
and [type] = ''d''
 
and backup_finish_date > ''' + @first_day_of_year + '''
 
and database_name in (select name from master.sys.databases)
 
group by
 
[database_name]
 
, convert(varchar(7),[backup_start_date],120)
 
order by
 
[database_name],convert(varchar(7),[backup_start_date],120);' + char(10) + char(10) from
 
sys.databases
 
where
 
database_id > 4
 
exec (@get_dbsize_history)
```
This information can then be trended using excel. For example; this is one of the Sharepoint Databases.

![Trend SQL Database Size](https://mikesdatawork.files.wordpress.com/2015/07/image0031.jpg "Visualize Database Size")
 


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

    
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

