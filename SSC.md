# Here We will talk about Fortify Software Security Center and its database installation and configuration.
> [!NOTE]
> Opentext Fortify supports many operating system [Supported platforms and architectures](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-platforms.htm?TocPath=System%2520requirements%257C_____2) and aslo supports many dbs [Supported Databases](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-db.htm?TocPath=System%2520requirements%257C_____4) but here you will install it on Windows Server and use SQL Server.

<br/>

### Install & Configure Microsoft SQL Server Database

> [!NOTE]
> There are hardware requirements for each version of SSC, so you can check it from here [Hardware-requirements](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-reqs.htm?TocPath=System%2520requirements%257C_____0)

+ [Download](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) & Install SQL Server
  + After finish installation click on Customize and choose Create new instance then write a name such as "SSCdb"
    + Choose Windows and SQL Authentication Mixed mode and add the machine user
+ Open SSMS with Windows Authentication
+ Create new user
  + Security -> Logins -> Right-click -> New Login -> SQL Authentication
+ Login with this new user
+ Create new database
  + Make Collation = SQL_Latin1_General_CP1_CS_AS
+ Enable
  + (AUTO_UPDATE_STATISTICS_ASYNC)
  + (ANSI_NULL_DFLT_ON)
  + (ALLOW_SNAPSHOT_ISOLATION)
  + (READ_COMMITTED_SNAPSHOT)
+ Open this path [Fortify_SSC_<version>\Fortify_<version>_Server_WAR_Tomcat.zip\sql\sqlserver\create-tables file]
  + Execute the create-tables file
+ Enable TCP/IP from SQL Server Configuration with port 1433
+ Disable Firewall or open port 1433

<br/>

## Install & Configure Fortify Software Security Center (SSC)
> [!NOTE]
> There are hardware requirements for each version of SSC, so you can check it from here [Hardware-requirements](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-reqs.htm?TocPath=System%2520requirements%257C_____0)

+ Download SSC file “Fortify_SSC_Server_23.2.0” and fortify.license from (https://sld.microfocus.com/) and extract it.
+ Install java
+ Install Tomcat and you must create username, and password for it.
+ Copy Fortify_SSC_<version>/Fortify_<version>_Server_WAR_Tomcat.zip/ssc.war file to C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps
+ Create new folder [C:\Fortify]
+ Open Apache configuration -> Java -> Java Option [-Dfortify.home=C:\Fortify] -> Initial memory pool [2048] -> Maximum memory pool [4096] -> Apply > Save
+ Configure SSL/TLS Certificate to use https
  ```
  cd C:\Program Files\Java\jdk-17\bin
  ```
  ```
  keytool -genkey -alias mykey -keyalg RSA -keystore C:\mykeystore
  ```
  ```
  keytool -export -alias mykey -keystore C:\mykeystore -file "mycert.cer"
  ```
  + Edit tomcat/conf/server.xml
    ```
    <Connector port="8443" maxThreads="200"
    scheme="https" secure="true" SSLEnabled="true"
    keystoreFile="<mykeystore>" keystorePass="<mypassword>"
    clientAuth="false" sslProtocol="TLS"/>
    ```
  + cd C:\Program Files\Apache Software Foundation\Tomcat 9.0\bin
  + startup
+ Configure Tomcat to use secure and HttpOnly flags
  + Edit tomcat/conf/web.xml
    ```
    <session-config>
    <cookie-config>
    <http-only>true</http-only>
    <secure>true</secure> </cookie-config>
    </session-config>
    ```
+ Open https://[IP]:8443/ssc on browser
> [!NOTE]
> You can use a hostname instead of IP by configure the hosts file [C:\Windows\System32\drivers\etc\hosts]
  ```
  [IP]         [hostname]
  #Example:
  10.10.3.6    sscmanager.com
  ```
+ Put token key from C:\Fortify\init.token, Next
+ Upload the license, Next
+ SSC URL: ```https://[IP]:8443/ssc```, Next
+ Choose SQL Server
+ Write Username and password of database
+ JDBC URL: ```jdbc:sqlserver://[database-IP]:1433;database=[database-name];encrypt=true;trustServerCertificate=true;rewriteBatchedStatements=true;sendStringParametersAsUnicode=false```
+ Click on Test Connection, Next
+ Click Browse and select [Upload one-by-one and after every one click Seed Database], Next
  + Fortify_Process_Seed_Bundle-2023_Q1_<build>.zip file (Required)
  + Fortify_Report_Seed_Bundle-2023_Q1_<build>.zip file (Required)
  + Fortify_PCI_SSF_Basic_Seed_Bundle-2023_Q1_<build>.zip file (Optional)
  + Fortify_PCI_Basic_Seed_Bundle-2023_Q1_<build>.zip file (Optional)
+ Restart Apache and the browser [Or restart the machine]
+ Login with admin, admin
  + Note: SSC get Credentials from the database
  + He will ask you to change the password

<br/>

### Configuration
+ Create new administrator account, and then delete the default administrator
+ Update Rulepacks from server from Administration -> Metrics & Tracking -> Rulepacks then click UPDATE FROM SERVER -> Okay
+ After finish the installation, you can create a banner for your organization on (Logon, Dashboard, Applications, Reports, and so on) [Link](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SSC_Help_23.2.0/index.htm#SSC_UG/Custom_Banner.htm?TocPath=Part%2520I%253A%2520Deploying%2520Fortify%2520Software%2520Security%2520Center%257CAdditional%2520Fortify%2520Software%2520Security%2520Center%2520Configuration%257C_____6)
+ Changing the Support Contact Link in the About Fortify SSC Box with support portal for your organization [Link](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SSC_Help_23.2.0/index.htm#SSC_UG/Customize_Support.htm?TocPath=Part%2520I%253A%2520Deploying%2520Fortify%2520Software%2520Security%2520Center%257CAdditional%2520Fortify%2520Software%2520Security%2520Center%2520Configuration%257C_____9)
+ Edit password required from password.strength.min.score property (located in <fortify.home>/<app_context>/conf/app.properties)
+ Enable Export to CSV from Administrator -> Configuration -> Core to export Fortify SSC data displayed in the Dashboard and AUDIT views.
+ Enable Priority Override from SSC -> ADMINISTRATION -> Configuration -> Issue Audit to can edit severity of vulns




