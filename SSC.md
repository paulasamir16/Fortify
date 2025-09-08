# Here, we will talk about Fortify Software Security Center and its database installation, configuration and upgrade
> [!NOTE]
> Opentext Fortify supports many operating system [Supported platforms and architectures](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-platforms.htm?TocPath=System%2520requirements%257C_____2) and aslo supports many dbs [Supported Databases](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-db.htm?TocPath=System%2520requirements%257C_____4) but in this guide we will install it on Windows Server and use SQL Server

> [!NOTE]
> There are three methods to deploy Fortify Software Security Center (SSC)
> 1. Manual (Here, we talk about this method)
> 2. [Automated](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#adv-config/auto-config.htm)
> 3. [In Kubernetes](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#deploy-ssc/deploy-kubernetes.htm?TocPath=Deploying%2520Fortify%2520Software%2520Security%2520Center%257C_____5)

<br/>

### Install & Configure Microsoft SQL Server Database

> [!NOTE]
> There are hardware requirements for each version of SSC, so you can check it from here — [Hardware-requirements](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-reqs.htm?TocPath=System%2520requirements%257C_____0)

+ Must give static IP to database machine because we connect from SSC machine to database machine with jdbc
  + Or you can use hostname instead of IP
+ [Download](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) & Install SQL Server
  + After finish installation click on Customize, choose Create new instance then write a name such as ```SSCdb```
    + Choose Windows and SQL Authentication Mixed mode and add the machine user
+ Open SSMS with Windows Authentication
+ Create new user with this permission — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#deploy-ssc/db-account-perms.htm)
  + Security -> Logins -> Right-click -> New Login -> SQL Authentication
+ Login with this new user
+ Create new database
  + Set the database collation to ```SQL_Latin1_General_CP1_CS_AS```
+ Enable
  + ```(AUTO_UPDATE_STATISTICS_ASYNC)```
  + ```(ANSI_NULL_DFLT_ON)```
  + ```(ALLOW_SNAPSHOT_ISOLATION)```
  + ```(READ_COMMITTED_SNAPSHOT)```
+ Open this path ```Fortify_SSC_<version>\Fortify_<version>_Server_WAR_Tomcat.zip\sql\sqlserver\``` and execute the ```create-tables``` file
+ Enable TCP/IP from ```SQL Server Configuration``` with port 1433
+ Either disable Firewall or open port 1433

<br/>

## Install & Configure Fortify Software Security Center (SSC)
> [!NOTE]
> There are hardware requirements for each version of SSC, so you can check it from here — [Hardware-requirements](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-reqs.htm?TocPath=System%2520requirements%257C_____0)

+ Download SSC file ```Fortify_SSC_Server_23.2.0``` and ```fortify.license``` from [Opentext Portal](https://sld.microfocus.com/) then extract it
+ Install java with required version
+ Install Tomcat and you must enable ```Host Manager``` then create username & password for it
+ Copy ```Fortify_SSC_<version>/Fortify_<version>_Server_WAR_Tomcat.zip/ssc.war``` file to ```C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps```
+ Create new folder, for example ``C:\Fortify``
+ Open Apache configuration -> Java -> Java Option ```-Dfortify.home=C:\Fortify``` -> Initial memory pool ```2048``` -> Maximum memory pool ```4096``` -> Apply > Save
+ Configure SSL/TLS Certificate to use https
  + Open cmd
    ```
    cd C:\Program Files\Java\jdk-17\bin
    ```
    ```
    keytool -genkey -alias mykey -keyalg RSA -keystore C:\mykeystore
    ```
    ```
    keytool -export -alias mykey -keystore C:\mykeystore -file "sscert.cer"
    ```
  + Edit tomcat/conf/server.xml with any text editor
    ```
    <Connector port="8443" maxThreads="200"
    scheme="https" secure="true" SSLEnabled="true"
    keystoreFile="<mykeystore>" keystorePass="<mypassword>"
    clientAuth="false" sslProtocol="TLS"/>
    ```
  + Open cmd
    ```
    cd "C:\Program Files\Apache Software Foundation\Tomcat 9.0\bin"
    ```
    ```
    startup
    ```
+ Configure Tomcat to use ```secure``` and ```HttpOnly``` flags
  + Edit ```tomcat/conf/web.xml```
    ```
    <session-config>
    <cookie-config>
    <http-only>true</http-only>
    <secure>true</secure> </cookie-config>
    </session-config>
    ```
+ Open ```https://<IP>:8443/ssc``` on browser
> [!NOTE]
> You can use a hostname instead of IP by configure the hosts file ```C:\Windows\System32\drivers\etc\hosts```
>  ```
>  [IP]         [hostname]
> 
>  10.10.3.6    sscmanager.com
>  ```
+ Put token key from ```C:\Fortify\init.token```, Next
+ Upload the license, Next
+ SSC URL: ```https://<IP>:8443/ssc```, Next
+ Choose ```SQL Server```
+ Write ```Database Username``` and ```Database Password``` of database
+ JDBC URL: ```jdbc:sqlserver://[database-IP]:1433;database=[database-name];encrypt=true;trustServerCertificate=true;rewriteBatchedStatements=true;sendStringParametersAsUnicode=false```
+ Click on ```Test Connection```, Next
+ Click **Browse** and select the Bundle file (Upload one-by-one and after every one click **Seed Database**), then click Next after finishing
  + ```Fortify_Process_Seed_Bundle-2023_Q1_<build>.zip``` file (Required)
  + ```Fortify_Report_Seed_Bundle-2023_Q1_<build>.zip``` file (Required)
  + ```Fortify_PCI_SSF_Basic_Seed_Bundle-2023_Q1_<build>.zip``` file (Optional)
  + ```Fortify_PCI_Basic_Seed_Bundle-2023_Q1_<build>.zip``` file (Optional)
> [!NOTE]
> The seed bundle files are included in the Fortify Software Security Center installation package. After your initial deployment, you can download off-cycle seed bundles from the [Application Security Customer Portal](https://support.fortify.com/secure/index.jsp) under the PREMIUM CONTENT > FORTIFY EXCHANGE
+ Restart Apache and the browser [Or restart the machine]
+ Login with admin & admin then you will prompte to change the password
  + Note: SSC stores and retrieves Credentials from the database

<br/>

### Configuration
+ **Create new administrator account, and then delete the default administrator**
+ **Update Rulepacks from server from Administration -> Metrics & Tracking -> Rulepacks then click UPDATE FROM SERVER -> Okay**
+ **Creating a system-wide banner**
> [!NOTE]
> As an Administrator, you can create a system-wide banner that is displayed centered below the header on all pages in the application
  + To create a system-wide banner
    + Sign in to SSC as an Administrator
    + On the header, select Administration
    + On the navigation pane, expand Configuration, and then select Customization
    + Under Customized Banner, select the Display a custom banner system-wide check boxIn the Enter the text to display in the banner box, type the text for your banner
    + Click SAVE
+ **Changing the Support Contact Link in the About Fortify SSC Box with support portal for your organization**
  + Sign in to SSC as an Administrator
  + On the header, select Administration
  + On the navigation pane, expand Configuration, and then select Customization
  + Select the Enable using the support URL for your organization in the About box check box
  + In the Support URL for your organization box, enter the web address for your organization's support portal
  + In the Text displayed for your support URL box, type the text to display for the link to your organization's support portal
  + Click SAVE
+ **Setting the required password strength for Fortify Software Security Center sign in**
  + You can use the ```password.strength.min.score``` property (Located in ```<fortify.home>/<app_context>/conf/app.properties```)
    + 0 - Poor
    + 1	- Weak
    + 2	- Medium
    + 3	- Strong
    + 4	- Very strong
+ **Blocking data export to CSV files**
  + To prevent users from exporting Fortify Software Security Center data to CSV files
    + Sign in to SSC as an Administrator
    + On the header, select Administration
    + On the navigation pane, expand Configuration, and then select Core
    + Clear the Enable Export to CSV from the Dashboard and AUDIT views check box
    + Click SAVE
+ **If you purchased Fortify Insight, you can add a Fortify Insight link to your Dashboard**
  + To add the Fortify Insight link to your Dashboard view
    + Sign in to SSC as an Administrator
    + On the header, select Administration
    + On the navigation pane, expand Configuration, and then select Customization
    + Under Fortify Insight URL, select the Enable display of the Fortify Insight URL on your Dashboard check box
    + In the Fortify Insight URL box, enter the URL for your Fortify Insight page
    + Click SAVE
+ **Enabling the running and management of OpenText ScanCentral DAST scans (Enable integration with OpenText ScanCentral DAST with SSC)**
  + Enable OpenText ScanCentral DAST integration in Fortify Software Security Center by doing the following
    + Sign in to SSC as an Administrator
    + On the header, select Administration -> Configuration, and then select ScanCentral DAST
    + On the ScanCentral DAST page, select the Enable ScanCentral DAST check box
    + In the ScanCentral DAST server URL box, type your OpenText ScanCentral DAST server URL
    + The OpenText ScanCentral DAST server URL should resemble one of the following
      + ```http://<DAST_API_Host>:<port>/api/```
      + ```http://<DAST_API_IP>:<port>/api/```
        + You can use the https protocol instead
      + Click SAVE

#### Additional Configuration
+ **[Configuring core settings](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/config-core.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257CConfiguring%2520core%2520settings%257C_____0)**
+ **You can create a banner for your organization on (Logon, Dashboard, Applications, Reports, and so on) — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/custom-banner.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____10)**
+ **Configuring email alert notification settings — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/Config_Email_Alerts.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257CConfiguring%2520email%2520alert%2520notification%2520settings%257C_____0)**
+ **[Enabling the priority override capability](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#auditing/Priority_Override.htm?TocPath=Collaborative%2520auditing%257CAuditing%2520analysis%2520results%257C_____4)**
+ **[Configuring Issue Stats thresholds](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/Config_Issue_Age.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____2)**
+ **[Configuring application security training](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/config_training.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____3)**
+ **[Configuring Java Message Service settings](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/Config_JMS_settings.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____14)**
+ **[Configuring a proxy for integrations](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/Config_proxy.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____18)**
+ **[Setting the strategy for resolving issue audit conflicts](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#config-ssc/Aud_Conflict_Strat.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____13)**
+ **Supported service integrations**
  + [Reference-One](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#sys-reqs/ssc-service-integrations.htm?TocPath=System%2520requirements%257C_____9)
  + [Reference-Two](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#deploy-ssc/deploy-overview.htm?TocPath=Deploying%2520Fortify%2520Software%2520Security%2520Center%257C_____1)
  + [Reference-Three](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#deploy-ssc/components.htm?TocPath=Additional%2520Fortify%2520Software%2520Security%2520Center%2520configuration%257C_____1)

<br/>

## Fortify Software Security Center Upgrading
+ Determine the installed version and the upgraded version that you want to upgrade to it
+ Open the documantation for the upgraded version to see the upgrading requirements

> **Example**
> + If you have an installed SSC version 24.4.x or 24.2.x, you can upgrade to them 25.2.x
>   + [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2520/ssc-ugd-html-25.2.0/index.htm#upgrade-ssc/upgrade-ssc.htm?TocPath=Upgrading%2520Fortify%2520Software%2520Security%2520Center%257C_____0)
