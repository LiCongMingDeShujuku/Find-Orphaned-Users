![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 查找孤立用户
#### Find Orphaned Users
**发布-日期: 2018年3月30日 (评论)**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
使用此逻辑（logic），你将能够找到孤立用户，并且还可以重新同步登录，删除登录，删除架构以及在必要时删除角色。首先在resynch列中运行所有代码，然后再运行该程序通常是个不错的做法。 因为它将为你提供简化列表。


## English
With this logic you’ll be able to find orphaned users, and also be provided with the logic to resynch the logins, drop the logins, drop the schema, and drop the roles if necessary. It’s generally a good idea to run everything in the resynch column first; then run the process again. This will provide you with a reduced list.

---
## Logic
```SQL
use master;
set nocount on
 
if object_id('tempdb..#orphaned_users') is not null
drop table  #orphaned_users
 
create table    #orphaned_users 
( 
    [database_name] varchar(255)
,   [user_name] varchar(255)
,   [resynch_login] varchar(3000)
,   [drop_login]    varchar(3000)
,   [drop_role] varchar(3000)
,   [drop_schema]   varchar(3000)
);
declare @find_orphaned_users varchar(max);
if (select right(substring(@@version, 0, charindex('(', @@version, 0)), 5) as int) < 2012
    begin
        set @find_orphaned_users = 
        'select 
            ''?'' [databasename]
        ,   sdp.[name]
        ,   ''use [?]; exec sp_change_users_login ''''update_one'''', ''''''  + sdp.name + '''''', ''''''  + sdp.name + ''''''; ''
        ,   ''use [?]; drop login ['' + sdp.name + ''];''
        ,   ''use [?]; alter authorization on schema::['' + sdp.name + ''] to [dbo];''
        ,   ''use [?]; alter authorization on role::['' + sdp.name + ''] to [dbo];''
        from 
            ?.sys.database_principals sdp left outer join sys.server_principals ssp on sdp.sid=ssp.sid
        where 
            sdp.type = ''s'' 
            and sdp.name not in (''dbo'',''sys'',''information_schema'',''guest'') 
            and sdp.name not like ''%##%'' 
            and db_id(''?'') > 4
            and ssp.sid is null';
        end;
        else
        begin
        set @find_orphaned_users = 
        'select 
            ''?'' [databasename]
        ,   sdp.[name]
        ,   ''use [?]; exec sp_change_users_login ''''update_one'''', ''''''  + sdp.name + '''''', ''''''  + sdp.name + ''''''; ''
        ,   ''use [?]; drop login ['' + sdp.name + ''];''
        ,   ''use [?]; alter authorization on schema::['' + sdp.name + ''] to [dbo];''
        ,   ''use [?]; alter authorization on role::['' + sdp.name + ''] to [dbo];''
        from 
            ?.sys.database_principals sdp left outer join sys.server_principals ssp on sdp.sid=ssp.sid
        where 
            sdp.type = ''s'' 
            and sdp.name not in (''dbo'',''sys'',''information_schema'',''guest'') 
            and sdp.name not like ''%##%'' 
            and db_id(''?'') > 4
            and ssp.sid is null 
            and sdp.authentication_type=1';
    end;
 
insert into #orphaned_users exec sp_msforeachdb @find_orphaned_users;
 
select * from #orphaned_users order by [database_name] asc;


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

