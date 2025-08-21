

Install & Configure Microsoft SQL Server Database
+ Download & Install SQL Server 2022
  + Create new instance
  + Choose Windows and SQL Authentication Mixed mode
+ Open SSMS with Windows Authentication
+ Create new user
  + Security -> Logins -> Right-click -> New Login -> SQL Authentication
+ Create new database
  + Make Collation = SQL_Latin1_General_CP1_CS_AS
+ Enable
  + (AUTO_UPDATE_STATISTICS_ASYNC)
  + (ANSI_NULL_DFLT_ON)
  + (ALLOW_SNAPSHOT_ISOLATION)
  + (READ_COMMITTED_SNAPSHOT)
+ Open this path [Fortify_SSC_<version>\Fortify_<version>_Server_WAR_Tomcat.zip\sql\sqlserver\create-tables file]
  + Execute the create-tables file
+ Enable TCP/IP from SQL Server Configuration
