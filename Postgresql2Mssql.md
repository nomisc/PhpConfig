### Access MSSQL DB from Postgresql DB ###

Use  https://wiki.postgresql.org/wiki/Foreign_data_wrappers

#### 1. step: install extension on Postgresql DB. 

info: 
https://github.com/tds-fdw/tds_fdw
https://github.com/tds-fdw/tds_fdw/blob/master/InstallUbuntu.md

###### Install freetds:
```
sudo apt-get update
sudo apt-get install libsybdb5 freetds-dev freetds-common
```

###### build from repository: 
```
sudo apt-get install git
git clone https://github.com/tds-fdw/tds_fdw.git
cd tds_fdw
make USE_PGXS=1
sudo make USE_PGXS=1 install
```

```
sudo service postgresql start
```

###### login in DB from terminal: 
```
sudo su 
su postgres
psql 
CREATE EXTENSION tds_fdw;
```

#### 2. step: create remote server, table, user mapping, import schema 

###### create server: 
```
CREATE SERVER remote_server	FOREIGN DATA WRAPPER tds_fdw	OPTIONS (servername 'xxx.xxx.xxx.xxx', port 'dbPort', database 'dbName', tds_version '7.1');
```

###### create foreign table:
```
CREATE FOREIGN TABLE f_table (	field1 varchar) SERVER remote_server OPTIONS (query 'select [MssqlField1] as field1 from mssqlDbTable', row_estimate_method 'showplan_all');
```

###### create user mapping: 
```
CREATE USER MAPPING FOR username SERVER remote_server OPTIONS (username 'dbUsername', password 'dbPassword'); 
```
###### import schema: 
```
CREATE SHEMA LocalSchema;
IMPORT FOREIGN SCHEMA "RemoteSchemaName" FROM SERVER remote_server INTO LocalSchema;
``` 